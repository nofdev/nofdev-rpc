The desired workflow:

1. service owner design API interfaces using their fav language

	For example, Jon snow is a groovy developer, he now is in duty of writing a `BlackCastle` module, he is planning to add an facade API interface `LordCommanderFacade`.

1. new interface modification commit to a dev branch

	Jon start a new branch on the project git repository. And the new facade API interface is look like following:

		package org.sevenkingdom.blackcastle

		/**
		 * A lord commander in black castle is the man in charge.
		**/
		@FacadeAPI
		interface LordCommanderFacade {
			/**
			 * commands someone to do the action, and take the consequence
			 * @param whom the command target
			 * @param action the command detail
			 * @returns the action result
			 * @throws BetrayalException when the target betrayed the commander
			**/
			ConsequenceDTO command(string whom, ActionDTO action)

			...

		}

	*DTO defination ommited*

1. submit the branch to be reviewed
1. tag the new version of reviewed API
1. commit hook processes the new API version
	1. parse API interface source code to `JSON-IDL`

	```
	groovy2jsonidl src/org/sevenkingdom/blackcastle
	```
	will out put
	```json
	{
		"meta": {},
		"interfaces": [
			{
				"package": "org.sevenkingdom.blackcastle",
				"name": "LordCommanderFacade",
				"doc": "A lord commander in black castle is the man in charge.",
				"meta": [
					{
						"type": "org.nofdev.rpc.FacadeAPI",
						"args": {}
					},
				],
				"methods": [
					{
						"name": "command",
						"doc": "commands someone to do the action, and take the consequence",
						"throws": [
							{
								"type": "org.sevenkingdom.blackcastle.BetrayalException",
								"doc": "when the target betrayed the commander"
							}
						],
						"return": {
							"type": "org.sevenkingdom.blackcastle.ConsequenceDTO",
							"doc": "the action result"
						},
						"args": [
							{
								"name": "whom",
								"type": "string",
								"doc": "the command target"
							},
							{
								"name": "action",
								"type": "org.sevenkingdom.blackcastle.ActionDTO",
								"doc": "the command detail"
							}
						]
					}
				]
			},
		],
		"types": [
			{
				"package": "org.sevenkingdom.blackcastle",
				"name": "ActionDTO",
				"doc": "a command action detail",
				"meta": {},
				"properties": [
					{
						"name": "...",
						"type": "...",
						"doc": "..."
					}
				]
			},
		],
	}
	```

	1. save this `JONS-IDL` as a module description file `org.sevenkingdom.blackcastle.module.idl.json`.
	1. push this file to central API managment console.

	```bash
	groovy2jsonidl src/org/sevenkingdom/blackcastle | ff soa idl push --group sevenkingdom --project blackcastle --json --file -
	```

	1. the API managment console will generate all language bindings of this API module.

	```bash
	jsonidl2ts sevenkingdom.blackcastle.module.idl.json
	npm build
	npm push

	jsonidl2groovy sevenkingdom.blackcastle.module.idl.json
	gradle publish

	jsonidl2csharp sevenkingdom.blackcastle.module.idl.json
	msbuild
	nuget publish
	```

1. and finally, all other project can consume this API module easily.

	```bash
	npm install --save sevenkingdom-blackcastle
	```
