# Community example & Pre-built integrations

Whether you've made personal Action integrations for your own workflow that may be a source of inspiration to others, or pre-built integrations that can be distributed to other users, be sure to submit a pull request [to this repo](https://github.com/Mindsense/Mail-Pilot-Support)'s `docs/examples.md` file, or email them to us at `founders@mindsense.co`.

?> If you want to submit a pull request with your example or pre-built integration, copy a section that you can use as a template to add it to this page.

---

### Add a note to Bear with tags

This action will create a new note in Bear filled with the entire plaintext contents of the email, and the note will have the tag "email" applied to it.

<a href="exampleFiles/bear1.json" download>Download the integration JSON file</a>

<details>
<summary>See the code (click to expand)</summary>

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

</details>

---

### Add a task to Things 3

This action will open Things 3 with quick entry dialogue for you to add the task title. The task notes will include information about the referencing message, as well as a link to open the message in Mail Pilot.

<a href="exampleFiles/things1.json" download>Download the integration JSON file</a>

<details>
<summary>See the code (click to expand)</summary>

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

?> You can check out all of the query items you can add at https://support.culturedcode.com/customer/en/portal/articles/2803573

</details>

?> See ideas for adding multiple, project-specific Things integrations on "Build & Customize" under "Examples of personalizing actions"

---
