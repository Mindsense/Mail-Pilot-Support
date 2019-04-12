# Community example & pre-built integrations

Whether you've made personal Action integrations for your own workflow that may be a source of inspiration to others, or pre-built integrations that can be distributed to other users, be sure to submit a pull request [to this repo](https://github.com/Mindsense/Mail-Pilot-Support)'s `docs/examples.md` file, or email them to us at `founders@mindsense.co`.

If you want to submit a pull request with your example or pre-build integration, here is an example template you can use to add it to this page:

---

### Add a note to Bear with tags

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

---
