---
title: Update a rule in a ruleset
pcx-content-type: reference
order: 786
type: overview
---

# Update a rule in a ruleset

Applies one or more changes to an existing rule in a ruleset at the account or zone level.

Use one of the following API endpoints:

| Operation | Method + Endpoint |
|-----------|-------------------|
| [Patch an individual rule][ur-account] (account) | `PATCH /accounts/{account-id}/rulesets/{ruleset-id}/rules/{rule-id}` |
| Patch an individual rule (zone) | `PATCH /zones/{zone-id}/rulesets/{ruleset-id}/rules/{rule-id}` |

[ur-account]: https://api.cloudflare.com/#account-rulesets-patch-an-individual-rule

You can update the definition of the rule, changing its fields, or change the order of the rule in the ruleset. Invoking this method creates a new version of the ruleset.

## Update the definition of a rule

To update the definition of a rule, include the new rule definition in the request body. You must include all the rule fields that you want to be part of the new rule definition, even if you are not changing their values.

<details open>
<summary>Request</summary>
<div>

```json
curl -X PATCH \
-H "X-Auth-Email: user@example.com" \
-H "X-Auth-Key: REDACTED" \
"https://api.cloudflare.com/client/v4/accounts/{account-id}/rulesets/{ruleset-id}/rules/{rule-id-1}" \
-d '{
  "action": "js_challenge",
  "expression": "(ip.geoip.country eq \"GB\" or ip.geoip.country eq \"FR\") or cf.threat_score > 0",
  "description": "challenge GB and FR or based on IP Reputation"
}'
```

</div>
</details>

The response includes the complete ruleset after updating the rule.

<details>
<summary>Response</summary>
<div>

```json
{
  "result": {
    "id": "{ruleset-id}",
    "name": "Custom Ruleset 1",
    "description": "My first custom ruleset",
    "kind": "custom",
    "version": "11",
    "rules": [
      {
        "id": "{rule-id-1}",
        "version": "2",
        "action": "js_challenge",
        "expression": "(ip.geoip.country eq \"GB\" or ip.geoip.country eq \"FR\") or cf.threat_score > 0",
        "description": "challenge GB and FR or based on IP Reputation",
        "last_updated": "2021-03-22T12:54:58.144683Z",
        "ref": "rule-ref-1",
        "enabled": true
      },
      {
        "id": "{rule-id-2}",
        "version": "1",
        "action": "challenge",
        "expression": "not http.request.uri.path matches \"^/api/.*$\"",
        "last_updated": "2020-11-23T11:36:24.192361Z",
        "ref": "{rule-ref-2}",
        "enabled": true
      }
    ],
    "last_updated": "2021-03-22T12:54:58.144683Z",
    "phase": "http_request_firewall_custom"
  },
  "success": true,
  "errors": [],
  "messages": []
}
```

</div>
</details>

## Change the order of a rule in a ruleset

To reorder a rule in a list of ruleset rules, include a `position` field in the request, containing one of the following arguments:

* `"before": "{rule-id}"` — Places the rule before rule `{rule-id}`. Use this argument with an empty rule ID value (`""`) to set the rule as the first rule in the ruleset.

* `"after": "{rule-id}"` — Places the rule after rule `{rule-id}`. Use this argument with an empty rule ID value (`""`) to set the rule as the last rule in the ruleset.

* `"index": {position-number}` — Places the rule in the exact position specified by the integer number `{position-number}`. Position numbers start with `1`. Existing rules in the ruleset from the specified position number onward are shifted one position (no rule is overwritten). For example, when you place a rule in position <var>n</var> using `index`, existing rules with index <var>n</var>, <var>n</var>+1, <var>n</var>+2, and so on, are shifted one position — their new position will be <var>n</var>+1, <var>n</var>+2, <var>n</var>+3, and so forth. If the index is out of range, the method returns 400 HTTP Status Code.

<Aside type='warning' header='Important'>

You can only use one of the arguments `before`, `after`, and `index` at a time.

</Aside>

Reorder a rule without changing its definition by including only the `position` field in the `PATCH` request body. You can also update a rule definition and reorder it in the same `PATCH` request by including both the `rule` field and the `position` field.

The following examples build upon the following (abbreviated) ruleset:

```json
{
  "rules": [
    { "id": "{rule-id-1}" },
    { "id": "{rule-id-2}" },
    { "id": "{rule-id-3}" },
    { "id": "{rule-id-4}" }
  ]
}
```

### Example #1

The following request with the `position` field places rule `{rule-id-2}` as the first rule:

```json
---
header: Request
---
curl -X PATCH \
-H "X-Auth-Email: user@example.com" \
-H "X-Auth-Key: REDACTED" \
"https://api.cloudflare.com/client/v4/zones/{zone-id}/rulesets/{ruleset-id}/rules/{rule-id-2}" \
-d '{
  "position": {
    "before": ""
  }
}
```
In this case, the new rule order would be:

`{rule-id-2}`, `{rule-id-1}`, `{rule-id-3}`, `{rule-id-4}`

### Example #2

The following request with the `position` field places rule `{rule-id-2}` after rule 3:

```json
---
header: Request
---
curl -X PATCH \
-H "X-Auth-Email: user@example.com" \
-H "X-Auth-Key: REDACTED" \
"https://api.cloudflare.com/client/v4/zones/{zone-id}/rulesets/{ruleset-id}/rules/{rule-id-2}" \
-d '{
  "position": {
    "after": "{rule-id-3}"
  }
}
```

In this case, the new rule order would be:

`{rule-id-1}`, `{rule-id-3}`, `{rule-id-2}`, `{rule-id-4}`

### Example #3

The following request with the `position` field places rule `{rule-id-1}` in position 3, becoming the third rule in the ruleset:

```json
---
header: Request
---
curl -X PATCH \
-H "X-Auth-Email: user@example.com" \
-H "X-Auth-Key: REDACTED" \
"https://api.cloudflare.com/client/v4/zones/{zone-id}/rulesets/{ruleset-id}/rules/{rule-id-1}" \
-d '{
  "position": {
    "index": 3
  }
}
```

In this case, the new rule order would be:

`{rule-id-2}`, `{rule-id-3}`, `{rule-id-1}`, `{rule-id-4}`
