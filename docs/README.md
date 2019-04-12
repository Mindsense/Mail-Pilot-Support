# Mail Pilot Action Integrations

> Action integrations allow you to totally customize and create per-message actions made available in Mail Pilot's Action menu. This document shows you how to create and modify the JSON configuration files for these Action integrations.

## What are Action integrations?

Available on individual messages within Mail Pilot, the Action menu gives users a quick, searchable way to perform any available action on any message.

Action integrations are a way for you to modify items in the Action menu, as well as add entirely new actions to the menu.

You can add actions that integrate with your favorite apps, and you can even add multiple personalized actions for a single app.

Action integrations fire off specific URLs, triggering actions in other apps via their published URL schemes. They are compatible with any app with published URL schemes.

## Example use cases

Here are some things you can do with Action integrations:

* Add an action to save a message in your favorite note-taking app (like Bear; see example below)
* Add an action to create a task in your favorite task management app (like Things 3 or OmniFocus; see examples below)
* Add custom actions that create tasks in specific projects or lists within your favorite task management app (see the example of this with Things 3 below)
* Customize what an action does in your favorite app by adding custom fields (see an example of automatically adding tags to new notes in Bear below)
* Create shortcuts to automatically take specific actions in other apps (see an example of automatically setting priority in OmniFocus below)
* Distribute a discrete integrations file for an app you develop or love to use so that other Mail Pilot users can easily add these integrations to their Action menu
* Receive discrete integrations files from developers and other users in the community to easily add integrations to your Action menu

## Quick start

Want to just dive in and start tinkering? If you're using the most recent Mail Pilot 3 beta build, follow these steps to get started.

1. Open Finder and navigate to `~/Library/Application Support/com.mindsensehq.Mail-Pilot-HL/` (you can do this quickly in Finder by hitting `Command + Shift + G` or clicking `Go > Go to Folder...` and pasting in this folder path).
2. Create a new folder called `integrations`.
3. Create a JSON file in the new `integrations` folder. You can name it anything you want, but for the sake of this example, I'll assume you've called it `actions.json`.
4. In your new file, `~/Library/Application Support/com.mindsensehq.Mail-Pilot-HL/integrations/actions.json`, paste the following contents:

```json
{
	"actions": [
		{
			"id": "things",
			"appName": "Things",
			"actionTitle": "Create a to-do in Things",
			"actionQueries": [
				"things"
			],
			"urlTemplate": {
				"scheme": "things",
				"host": "",
				"path": "/add",
				"queryItems": {
					"show-quick-entry": "true",
					"notes": [
						"Referencing message from %(sender) \n\n",
						"Subject: \n%(subject)\n\n",
						"Open the message in Mail Pilot: \n%(openMessageUrl)\n\n",
						"Preview: \n%(textPreview)..."
					]
				}
			}
		}
	]
}

```

5. Open Mail Pilot, click `Help > Reload integrations`, and use the Action menu by selecting a message, and clicking "Action" at the bottom of the window. *(Note: You must be in the mininal layout to see the Action menu. To enable it, in preferences, click `Themes > Use minimal layout`.)*

6. Now, you can tinker. This example runs the `add` command in Things 3, but you can look up the URL schemes of your favorite apps and modify this integration to work with them.

?> **Made something cool?** Whether you've made personal Action integrations for your own workflow that may be a source of inspiration to others, or pre-built integrations that can be distributed to other users, be sure to submit a pull request [to this repo](https://github.com/Mindsense/Mail-Pilot-Support)'s `docs/examples.md` file, or email them to us at `founders@mindsense.co`.

?> **Want a simpler way to get started?** Stay tuned; we're on the very first release of Action integrations now. In the future, we will include new ways to find, enable, customize, and create Action integrations.

---

## Integration file format

Action integrations are stored as JSON files in Mail Pilot's `Application Support` directory (see "Integration file location" for more).

They must be valid JSON, and have the following syntax:

```json
{
	"actions": [
		{
			"id": "things",
			"appName": "Things",
			"actionTitle": "Create a to-do in Things",
			"actionQueries": [
				"things"
			],
			"urlTemplate": {
				"scheme": "things",
				"host": "",
				"path": "/add",
				"queryItems": {
					"show-quick-entry": "true",
					"notes": [
						"Referencing message from %(sender) \n\n",
						"Subject: \n%(subject)\n\n",
						"Open the message in Mail Pilot: \n%(openMessageUrl)\n\n",
						"Preview: \n%(textPreview)..."
					]
				}
			}
		}
	]
}

```

!> **Note:** If an integration file has invalid JSON, none of the integrations within it will be loaded by Mail Pilot, and this can potentially cause other integrations files to also not load.

## Fields

All fields are required.

### id

* Type: `String`
* Must be unique

The `id` field must be unique to each Action integration. It uniquely identifies the Action integration to Mail Pilot internally. Mail Pilot sorts Action integrations by their ID field, and shows the first three when the Action menu is initially opened.

!> **Note:** Make sure each integration has a unique id, paying special attention if you have multiple integrations for a single app to ensure that each has its own distinct `id` value.

```json
{
  "id": "things",
  ...
}
```

### appName

* Type: `String`

This is the name of the app which the integration is for. It does not need to match the app's name on your Mac, but if you have multiple integrations for a single app, their `appName` values should all be the same.

```json
{
  "appName": "Things",
  ...
}
```


### actionTitle

* Type: `String`

This is the title of the action as it will appear in your Action menu.

```json
{
  "actionTitle": "Create a to-do in Things",
  ...
}
```

?> Keep in mind: The Action menu is a set width, so action titles shouldn't be too long, otherwise they will get cut off.


### actionQueries

* Type: `Array` of `String` elements

This is a list of `String` queries to match against when text is entered into the Action menu filter field. In addition to matching against these queries, the Action menu will also match against the `actionTitle` and `appName` fields. If that is sufficient for your needs, you may pass an empty array here.

```json
{
  "actionQueries": [
    "things",
    "task",
    "to-do"
  ],
  ...
}
```

?> Adding a large number of items to this array may impair performance of the Action menu.


### urlTemplate

* Type: `Object`; see below.

Defines how the integration should interact with the receiving app. It is based on the receiving app's URL scheme. Its values can include template variables: placeholders which Mail Pilot will fill in using information from the message being acted upon.

Putting together a `urlTemplate` object requires knowing the receiving app's URL scheme, as well as how you want Mail Pilot to send message-specific data to that app.

As a basic template, when you look up URL schemes for receiving apps, you may see them in the following formats:
1. `scheme://host/path?parameters=values`
2. `scheme:///path?parameters=values`
3. `scheme://host?parameters=values`

Real-world examples corresponding to each of the above:
1. **Bear 1**: `bear://x-callback-url/create?title=My%20Note...`
  * `scheme`: `"bear"`
  * `host`: `"x-callback-url"`
  * `path`: `"/create"`
2. **Things 3**: `things:///add?show-quick-entry=true&title=sometitle...`
  * `scheme`: `"things"`
  * `host`: `""`
  * `path`: `"/add"`
3. **Todoist 7**: `todoist://addtask?content=somecontent`
  * `scheme`: `"todoist"`
  * `host`: `"addtask"`
  * `path`: `""`

!> Even when a field, such as `host` or `path`, is empty, it needs to be included.


```json
{
  ...,
  "urlTemplate": {
		"scheme": "things",
		"host": "",
		"path": "/add",
		"queryItems": {
			"show-quick-entry": "true",
			"notes": [
				"Referencing message from %(sender) \n\n",
				"Subject: \n%(subject)\n\n",
				"Open the message in Mail Pilot: \n%(openMessageUrl)\n\n",
				"Preview: \n%(textPreview)..."
			]
		}
	}
}
```

For a full breakdown of each field within the `urlTemplate` object, see below.


### urlTemplate.scheme

* Type: `String`

The scheme of the receiving app's URL. When you look up an app's URL configuration, you will see the scheme as follows:

`SCHEME://host/path?parameters=values`

```json
"urlTemplate": {
  "scheme": "things",
  ...
}
```

### urlTemplate.host

* Type: `String`

The host of the receiving app's URL. When you look up an app's URL configuration, you will see the `host` as follows:

`scheme://HOST/path?parameters=values`

You may also see URL schemes like these:

* `scheme://HOST?parameters=values` which simply does not have a `path`, but nothing is different for the `host`.
* `scheme:///path?parameters=values` in which case `host` should be set to an empty string (`"host": ""`).


```json
"urlTemplate": {
  "host": "x-callback-url",
  ...
}
```


### urlTemplate.path

* Type: `String`

The path of the receiving app's URL. When you look up an app's URL configuration, you will see the `path` as follows:

`scheme://host/PATH?parameters=values`

You may also see URL schemes like these:

* `scheme://host?parameters=values` in which case `path` should be set to an empty string (`"path": ""`).
* `scheme:///PATH?parameters=values` which simply does not have a `host`, but nothing is different for the `path`.


```json
"urlTemplate": {
  "path": "/add",
  ...
}
```

?> Paths should almost always begin with a `/`, but if there is no path, it should more than likely be an empty string `""` (with no slash).


### urlTemplate.queryItems

* Type: `Object`

These are the parameters you would like to add to the URL sent along to the receiving app.

The object's keys should be the parameter names, and the object's values should be what you want the parameters values to be set to.

The object's values can be of type `String` or an `Array` of `String` elements. If the latter is user, Mail Pilot will concatenate the `String` elements. You can use this to make your JSON template more readable. No newlines will be added automatically, so if you want them, be sure to add them yourself.

The `String` values can include template variables which Mail Pilot will automatically fill in using the data from the message being acted on.

When you look up an app's URL configuration, you will see the parameters as follows:

`scheme://host/path?PARAMETER1=VALUE1&PARAMETER2=VALUE2`

```json
"urlTemplate": {
  ...,
  "queryItems": {
    "show-quick-entry": "true",
    "notes": [
      "Referencing message from %(sender) \n\n",
      "Subject: \n%(subject)\n\n",
      "Open the message in Mail Pilot: \n%(openMessageUrl)\n\n",
      "Preview: \n%(textPreview)..."
    ]
  }
}
```

For all template variables available to be used in the `String` values, see "Template variables" below.

#### Using arrays instead of strings

For your `queryItems`, you can provide either a string or an array of strings. You can use an array of strings to make your template more readable in the JSON file.

Mail Pilot will simply concatenate the strings in the array; it is up to you to add newlines to the end of each string if you want to separate them into their own lines in the resulting text.

## Template variables

There are template variables which can be used in `queryItems` string values which Mail Pilot will replace with data from the message being acted upon.

| <p style="width: 200px; margin: 0; padding: 0;">Variable Keyword</p> | Description |
| ------------ | ----------- |
| `%(senderName)` | The name of the message's sender |
| `%(senderAddress)` | The email address of the message's sender |
| `%(sender)` | The name and email address of the message's sender in a standard string: `name <address>` |
| `%(subject)` | The message's subject |
| `%(openMessageUrl)` | The link to open the message in Mail Pilot |
| `%(textPreview)` | A truncated version of the message's plaintext without whitespace |
| `%(textFull)` | The message's plaintext unchanged (it includes all whitespace such as newlines, and it is not truncated; it may be very long) |


## Integration file location

Action integration JSON files are stored in:

* For Mail Pilot 3: <br />`~/Library/Application Support/com.mindsensehq.Mail-Pilot-HL/integrations/`
* For Mail Pilot Discovery Edition: <br />`~/Library/Application Support/com.mindsensehq.Mail-Pilot-DE/integrations/`

While each integration file can have as many integrations in it as you would like, you can also add as many separate integration files to your `integrations/` directory as you would like.

This makes it very simple to receive and use an integrations file from other users or app developers, because you can simply add their JSON file to your `integrations/` directory without making changes to any files.

This also makes it easy to keep your integrations clean. If you have created more than one integration for a single app, you can create a separate integration file just for that app's integrations. Or, you can use a separate file for each integration. This flexibility makes it much easier to reference and modify your created integrations.

The files *must* be valid JSON. If a file is not valid, not of the integrations within it will load into Mail Pilot, and this may cause other integrations files to fail to load as well.

## Multiple integrations per file

Your integration file can have multiple integrations in it, like so:

```json
{
	"actions": [
		{
			"id": "todoist",
			"appName": "Todoist",
			"actionTitle": "Create a task in Todoist",
			"actionQueries": [
				"todoist"
			],
			"urlTemplate": {
				"scheme": "todoist",
				"host": "addtask",
				"path": "",
				"queryItems": {
					"content": "%(sender): %(subject)"
				}
			}
		}, {
			"id": "bear",
			"appName": "Bear",
			"actionTitle": "Create a note in Bear",
			"actionQueries": [
				"bear",
				"note"
			],
			"urlTemplate": {
				"scheme": "bear",
				"host": "x-callback-url",
				"path": "/create",
				"queryItems": {
					"title": "My note",
					"tags": "email",
					"text": [
						"Referencing message from %(sender) \n\n",
        		"Subject: \n%(subject)\n\n",
        		"Open the message in Mail Pilot: \n%(openMessageUrl)\n\n",
        		"Preview: \n%(textPreview)..."
					]
				}
			}
		}
	]
}
```

## Multiple integration files

Your integrations directory can have as many integration files within it.

This makes it very simple to receive and use an integrations file from other users or app developers, because you can simply add their JSON file to your `integrations/` directory.

It also makes it easy to keep your integrations clean. If you have created more than one integration for a single app, you can create a separate integration file just for that app's integrations. Or, you can use a separate file for each integration. This flexibility makes it much easier to reference and modify your created integrations.

## Reloading your integration files

Mail Pilot reloads your integration files at launch, but you can reload them while Mail Pilot is open by clicking `Help > Reload integrations`.

## Examples for popular apps

Here are some simple example integrations for popular apps. For more complex examples, see the next section.

### Things 3

This action will open Things 3 with quick entry dialogue for you to add the task title. The task notes will include information about the referencing message, as well as a link to open the message in Mail Pilot.

```json
{
	"actions": [
		{
			"id": "things",
			"appName": "Things",
			"actionTitle": "Create a to-do in Things",
			"actionQueries": [
				"things"
			],
			"urlTemplate": {
				"scheme": "things",
				"host": "",
				"path": "/add",
				"queryItems": {
					"show-quick-entry": "true",
					"notes": [
						"Referencing message from %(sender) \n\n",
						"Subject: \n%(subject)\n\n",
						"Open the message in Mail Pilot: \n%(openMessageUrl)\n\n",
						"Preview: \n%(textPreview)..."
					]
				}
			}
		}
  ]
}
```

?> You can check out all of the query items you can add for Things at https://support.culturedcode.com/customer/en/portal/articles/2803573

### Bear

This action will create a new note in Bear filled with the entire plaintext contents of the email, and the note will have the tag "email" applied to it.

```json
{
	"actions": [
		{
			"id": "bear",
			"appName": "Bear",
			"actionTitle": "Create a note in Bear",
			"actionQueries": [
				"bear",
				"note"
			],
			"urlTemplate": {
				"scheme": "bear",
				"host": "x-callback-url",
				"path": "/create",
				"queryItems": {
					"title": "%(sender): %(subject)",
					"tags": "email",
					"text": "%(textFull)"
				}
			}
		}
	]
}
```

?> You can check out all of the query items you can add for Bear at https://bear.app/faq/X-callback-url%20Scheme%20documentation/#create

### OmniFocus

This action will create a new task in OmniFocus.

```json
{
	"actions": [
		{
			"id": "omnifocus",
			"appName": "OmniFocus",
			"actionTitle": "Create a task in OmniFocus",
			"actionQueries": [
				"omnifocus"
			],
			"urlTemplate": {
				"scheme": "omnifocus",
				"host": "",
				"path": "/add",
				"queryItems": {
					"name": "%(sender): %(subject)",
					"note": [
						"Referencing message from %(sender) \n\n",
						"Subject: \n%(subject)\n\n",
						"Open the message in Mail Pilot: \n%(openMessageUrl)\n\n",
						"Preview: \n%(textPreview)..."
					]
				}
			}
		}
  ]
}
```

?> You can check out all of the query items you can add for OmniFocus at https://inside.omnifocus.com/url-schemes

### Todoist

This action will open the task creation dialogue in Todoist. Currently, Todoist does not allow for adding notes to tasks via URL schemes, so this Action integration will simply load some information into the title of the task, which you can then customize when you're adding the task.

```json
{
	"actions": [
		{
			"id": "todoist",
			"appName": "Todoist",
			"actionTitle": "Create a task in Todoist",
			"actionQueries": [
				"todoist"
			],
			"urlTemplate": {
				"scheme": "todoist",
				"host": "addtask",
				"path": "",
				"queryItems": {
					"content": "%(sender): %(subject)"
				}
			}
		}
  ]
}
```

?> You can check out all of the query items you can add for Todoist at https://developer.todoist.com/sync/v7/#tasks

## Examples of personalizing actions

Action integrations may be most powerful in how you can customize and personalize them. There are a lot of ways you can make Action integrations that are more powerful for the workflows that exist in your day, so that more can happen automatically for you.

Here are a series of examples to spark your imagination.

### Multiple Things actions

You could include multiple different integration actions for Things 3, where each one might do different things for you, such as filing the new task item into a specific project or area.

<details>
<summary>Code example (Click to expand)</summary>

```json
{
	"actions": [
		{
			"id": "things",
			"appName": "Things",
			"actionTitle": "Create a to-do in Things",
			"actionQueries": [
				"things"
			],
			"urlTemplate": {
				"scheme": "things",
				"host": "",
				"path": "/add",
				"queryItems": {
					"show-quick-entry": "true",
					"notes": [
						"Referencing message from %(sender) \n\n",
        		"Subject: \n%(subject)\n\n",
        		"Open the message in Mail Pilot: \n%(openMessageUrl)\n\n",
        		"Preview: \n%(textPreview)..."
					]
				}
			}
		}, {
			"id": "things-dev",
			"appName": "Things",
			"actionTitle": "Create a Dev to-do in Things",
			"actionQueries": [
				"things",
				"development"
			],
			"urlTemplate": {
				"scheme": "things",
				"host": "",
				"path": "/add",
				"queryItems": {
					"list": "Development Items",
					"show-quick-entry": "true",
					"notes": [
						"Referencing message from %(sender) \n\n",
        		"Subject: \n%(subject)\n\n",
        		"Open the message in Mail Pilot: \n%(openMessageUrl)\n\n",
        		"Preview: \n%(textPreview)..."
					]
				}
			}
		}
	]
}
```

</details>

### Include tags for notes in Bear

You could create actions that automatically add tags to new notes in Bear

<details>
<summary>Code example (Click to expand)</summary>

```json
{
	"actions": [
		{
			"id": "bear",
			"appName": "Bear",
			"actionTitle": "Create a note in Bear",
			"actionQueries": [
				"bear",
				"note"
			],
			"urlTemplate": {
				"scheme": "bear",
				"host": "x-callback-url",
				"path": "/create",
				"queryItems": {
					"title": "%(sender): %(subject)",
					"tags": "email,mailpilot",
					"text": "%(textFull)"
				}
			}
		}
	]
}
```

</details>

### Priority shortcuts in OmniFocus

You could create shortcuts that allow you to create tasks of different priorities in OmniFocus. For example, set your action query to `p1` to create a priority 1 task, `p2` to create a priority 2 task, and so on.

### Shortcuts for faster app access

You could create shortcuts that allow you to type even less to get the results you want. For example, you could add an action query of `xx` to rapidly pull up your most used app.

## Community Example & pre-built integrations

Whether you've made personal Action integrations for your own workflow that may be a source of inspiration to others, or pre-built integrations that can be distributed to other users, be sure to submit a pull request [to this repo](https://github.com/Mindsense/Mail-Pilot-Support)'s `docs/examples.md` file, or email them to us at `founders@mindsense.co`.

## Troubleshooting

* The `id` field must be unique. If you load multiple integrations with equal `id` strings, Mail Pilot will only show one of these actions, selecting which one at random.
* Your file must be valid JSON. If your integrations are not loading, run your file through a JSON validator to troubleshoot your syntax.
* All fields are required, even if they are meant to be empty strings or arrays. Ensure every field is included.
* See the section on URL schemes if you aren't sure how to break up an app's URL scheme into `scheme`, `host`, and `path` components.
* You can reload your integrations while Mail Pilot is running by clicking `Help > Reload integrations`.
