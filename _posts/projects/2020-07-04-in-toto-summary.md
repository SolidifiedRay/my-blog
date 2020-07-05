---
title: Introduction to in-toto
date: 2020-07-01
categories:
- Projects
tags:
- in-toto
- Security
- Python
---

This is a short abstract of [in-toto](https://in-toto.io/), a framework to secure the integrity of software supply chains. It is a project from [Secure System Lab](https://ssl.engineering.nyu.edu/) and I start participate in it since June 2020. 

First, what is a software supply chains and why is it important?  
![software supply chain](https://ssl.engineering.nyu.edu/img/projects/in-toto_diagram.png)
The software supply chain is a series of steps to make software can run in the end-users system. It may include steps such as test, build, and package. It is important because if someone compromise the supply chain, attacker can hide and distribute malware to a significant number of users, given the app's popularity. Microsoft has a well explained short video about supply chain attack, you can view it [here](https://docs.microsoft.com/en-us/windows/security/threat-protection/intelligence/supply-chain-malware).  

Then how can we protect the software supply chain? Citing from Microsoft's document above, one approach is to “deploy strong code integrity policies to allow only authorized apps to run.” The role of in-toto is similar to this. In-toto secures the software supply chain by specifying information of each step and who is authorized to perform each step.

I will use in-toto [demo](https://github.com/in-toto/demo) to help understand how in-toto works. In this demo, we need to perform all steps of a simple software supply chain: Alice is the **owner** of this project, Bob is the developer, and Carl packages the software. Bob and Carl can also be called as project **functionaries**, who will carry out the steps of the supply chain.  

First, Alice needs to create a **supply chain layout**. **Supply chain layout** is a signed file that includes the details and requirements of each step. It also includes a list of the **functionaries** who are in charge of carrying out each step. For this demo, it will include Bob's and Carl's public key. The layout of this demo will include the following data:      

The layout file will be signed with owner's private key. In this demo, it is Alice's private key.   
<pre>
	<code class="json">
		"signatures": [
			{
				"keyid": "556caebdc0877eed53d419b60eddb1e57fa773e4e31d70698b588f3e9cc48b35",
				"sig": "ad773a4d62d8e42cc3f6e6053089cd702d6928c9972f0b3d41f373bca5fe6971c5a1ed120256d37b06bbb60e4557a1b052fe5f81409881b51a8b4c663413d056101e14ae9f81e0a9ef8490f2c2be4f1aa55701de855120c5874a16ff36852e0d8b02fb07708620360f45353e4e1f5c14ac24e13d14ac8e41eb9519a4bcc33ee4405980f98bb30de0d726e114079277db3716790b1a099e20a323b7f66aafe306adbf208439860404759378906b2ee03ea95ae99ff677759b4f7b6822811185f1ea6931bcbf26638d2b86c26a40d4a076ddff0ddf1380302a254e24581e94873518d7cb529d505b6abc385f8991988d852095fe74331855ef584b7e06897e83d1740a580d8d3da66a8d97e9f6067a8172cf00b06d916104f59db059b5b10f319089b198ce6c7e7922509505c0808fafd7e635210b9b36be7acbf34a8f310947db332a7e1258313a1bfd38634c269667b4a0d216a463200a088ec2eda24fa2c34d44b22df1882c3ba3832519e27d77e815cf74075d1742731519b4acb866dbbde2"
			}
		]
	</code>
</pre>

The format of the layout will look like this:  

<pre>
	<code class="json">
		{ 
			"_type" : "layout",
			"expires" : "&lt;EXPIRES&gt;",
			"readme": "&lt;README&gt;",
			"keys" : {
				"&lt;KEYID&gt;" : "&lt;PUBKEY_OBJECT&gt;"
			},
			"steps" : [
				"&lt;STEP&gt;",
				"..."
			],
			"inspect" : [
				"&lt;INSPECTION&gt;",
				"..."
			]
		}
	</code>
</pre>

The "**expires**" section will specify the expiration date, and in default it will be one month after the layout is created. The "**steps**"" section contain a list of restrictions for each steps. The "**inspect**" section also contain a list of restrictions for each step within the link, but it will be used in the final verification by the client.  The "**keys**" section contain a list of public keys. In this demo, it will contains Bob's and Carl's public keys.  

To keep this blog short, I will only show the detail of **steps**. The **inspect** section is somehow similar to the **step** section. You can view the difference in in-toto [docs](https://github.com/in-toto/docs/blob/master/in-toto-spec.md#432-inspections).  

In this demo, Bob will clone foo.py from GitHub and update its version number.  
In the layout file, Alice will specify the "clone" step as the following:  

<pre>
	<code class="json">
		{
			"_type": "step",
			"expected_command": [
				"git",
				"clone",
				"https://github.com/in-toto/demo-project.git"
			],
			"expected_materials": [],
			"expected_products": [
				[
					"CREATE",
					"demo-project/foo.py"
				],
				[
					"DISALLOW",
					"*"
				]
			],
			"name": "clone",
			"pubkeys": [
				"776a00e29f3559e0141b3b096f696abc6cfb0c657ab40f441132b345b08453f5"
			],
			"threshold": 1
		}
	</code>
</pre>

The "**epxected_command**" field include the command that Bob will run to clone the code from GitHub. The "**expected_materials**" and "**expected_products**" fields contain a list of [ARTIFACT_RULES](https://github.com/in-toto/docs/blob/master/in-toto-spec.md#433-artifact-rules) to ensure that materials and products are not changed. Since Bob has nothing before he clone the code, there will be no **materials**. After cloning the code, Bob will create "foo.py" locally as **product**. The "**name**" field is just the name of this step. The "**pubkeys**" section includes Bob's public key. The "**thresehold**" field contains an integer specifying how many links are needed to verify this step.  

After Bob clone the code, a link file will be created:  

<pre>
	<code class="json">
	{
		"signatures": [
			{
			"keyid": "776a00e29f3559e0141b3b096f696abc6cfb0c657ab40f441132b345b08453f5",
			"sig": "a520c8cd74ab156d4bd1f1b9b12dd4d06d9c80cc10e2a82f7b0f3a08f62365d8133a6cc991df150912f6da790eec6d71573cbc613bf6b359d25806bccd2c50c78c198fc1b11f86bc261841e938ce8ca733247949dc2e9924181b8d21b1d36f4e58720c95de831ab7c88e1b637a9d7170e7e53f4188bad6370a8eb1a50dc6e6fab519dd29b1d45cbe71a5207906fa44b8b856a773b317921fa6967a8deb4b1732a6c9e93d73e87578277cdc7abe2c619cc1128c459e794bc4bedf8385c62b86f99c7320e913a6b290e7404e24ead2053046ca103e01b29970f9623e8959b1deca0acc897799d15cfaca689621cd30fde5bdbcf191ab48bea7dd9b4d8d9b8b81324a6f0456ba7343b5ae729cdca8c9c278a367465358741098aa67f13a148ca56f8456d8403b35640e2c829797c2f1f1e49e5a9d5881f465728949fbd24c110532056ec1c75fddd13304f76334d3551ba03feba926f94ae7c41ad50080a710a0fce703c5f6aeaa5a0b503b8235de80f8f3cc648880fc103b87bd73483d1891580f"
			}
		],
		"signed": {
			"_type": "link",
			"byproducts": {
			"return-value": 0,
			"stderr": "",
			"stdout": ""
			},
			"command": [
				"git",
				"clone",
				"https://github.com/in-toto/demo-project.git"
			],
			"environment": {},
			"materials": {},
			"name": "clone",
			"products": {
				"demo-project/foo.py": {
				"sha256": "ebebf8778035e0e842a4f1aeb92a601be8ea8e621195f3b972316c60c9e12235"
				}
			}
		}
	}
	</code>
</pre>

This link file will be singed with Bob's private keys. It also includes the name of the step, the products, the materials, Bob's command for cloning the code, and Bob's public key. 

After all the steps are carried out, the client will have the layout file and all the link files. Then the client can use these files to verify any unexpected changes in the supply chain. 

This is just a very rough illustration of how in-toto works, and I have to admit that I am not a good writer. I may come back to revise this blog sometime later. You can also check the full, well-explained document of in-toto [here](https://github.com/in-toto/docs/blob/master/in-toto-spec.md).