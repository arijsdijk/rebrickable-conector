# 🧱 Rebrickable Custom Connector

**Bring your LEGO catalog and collection to Microsoft Power Platform.**

This repository contains an OpenAPI definition and installation instructions for creating a custom connector for the **Rebrickable API v3**. Use the connector in Power Apps and Power Automate to search for sets and parts, view inventories, and manage your personal Rebrickable collection.

[Connector JSON](Rebrickable.swagger.json) · [Installation with screenshots](INSTALL.md) · [Rebrickable API documentation](https://rebrickable.com/api/v3/docs/)

## What can you do with it?

| Area | Connector capabilities |
| --- | --- |
| Sets | Search for sets, retrieve details, and view parts, minifigures, and alternate builds. |
| Parts | Search for parts and retrieve details, color variants, and associated sets. |
| Catalog | Browse colors, themes, part categories, elements, and minifigures. |
| Personal collection | Retrieve your sets, parts, minifigures, and lost parts. |

For example, build a Power App to browse sets, or a Power Automate flow that retrieves a set’s parts and adds them to your own overview.

## In this repository

| File | Contents |
| --- | --- |
| [Rebrickable.swagger.json](Rebrickable.swagger.json) | Importable Swagger 2.0 definition with actions, parameters, responses, and API key authentication. |
| [INSTALL.md](INSTALL.md) | Step-by-step import instructions with screenshots. |

## Getting started

### 1. Prerequisites

- A Rebrickable account with your own API key.
- Access to a Power Platform environment where you can create and use custom connectors.
- A local copy of [Rebrickable.swagger.json](Rebrickable.swagger.json).

### 2. Import the connector

1. Open [Power Apps](https://make.powerapps.com) and select your environment.
2. Go to **Custom connectors**.
3. Select **New custom connector** → **Import an OpenAPI file**.
4. Give the connector a name, such as `Rebrickable`.
5. Select `Rebrickable.swagger.json` and click **Continue**.
6. Review the settings and select **Create connector**.


### 3. Create a connection

The supplied definition sends the API key through the `Authorization` HTTP header. When creating the connection, enter the **complete value**:

```text
key YOUR_API_KEY
```

Replace `YOUR_API_KEY` with your own key. The word `key` and the space are required, as described in the [Rebrickable authentication documentation](https://rebrickable.com/api/v3/docs/).

| Setting | Value in the definition |
| --- | --- |
| API host | `rebrickable.com` |
| Base path | `/api/v3` |
| Protocol | HTTPS |
| Authentication | API key |
| Parameter name | `Authorization` |
| Parameter location | Header |

Store your own key in the connection; use placeholders in shared examples.

### 4. Test your first action

Open the **Test** tab, create or select your connection, and run this action:

| Field | Value |
| --- | --- |
| Action / operation ID | `lego_colors_list` |
| `page` | `1` |
| `page_size` | `10` |

A successful call returns HTTP `200` with a list of colors in `results`. You can then add the connector to your app or flow.

## Example actions

The operation IDs below come directly from the supplied JSON.

| Goal | Operation ID | Example input |
| --- | --- | --- |
| Search for sets | `lego_sets_list` | `search = Technic`, `page_size = 10` |
| View a set | `lego_sets_read` | `set_num = 75192-1` |
| Retrieve a set’s parts | `lego_sets_parts_list` | `set_num = 75192-1` |
| View a part | `lego_parts_read` | `part_num = 3001` |
| Retrieve themes | `lego_themes_list` | `page_size = 100` |
| Retrieve your set lists | `users_setlists_list` | `user_token = YOUR_USER_TOKEN` |

Actions with `user_token` require a user token in addition to your API key. The `users__token_create` action provides the `username` and `password` parameters for this purpose. Pass the returned token to the relevant user actions.

## Pagination and API usage

List actions use `page` and `page_size`. This definition sets the default page size to **100**, with a maximum of **1000**. List responses contain `count`, `next`, `previous`, and `results`.

The JSON does not include an `x-ms-pageable` configuration. To retrieve multiple pages, build a loop that increments `page` and stops when `next` is empty.

Rebrickable allows an average of **one request per second**. Slow down your flow when you receive HTTP `429` and respect the specified wait time. For full catalog downloads, use [Rebrickable’s CSV downloads](https://rebrickable.com/downloads/). See the [API documentation](https://rebrickable.com/api/v3/docs/) for usage rules.

## Troubleshooting

| Issue | What to check |
| --- | --- |
| `401 Unauthorized` | Is the API key valid, and does the connection value start with `key `? |
| `403 Forbidden` | Does the user have access to the requested data or action? |
| `404 Not found` | Is the supplied ID correct? For sets, use the full set number, including a suffix such as `-1`. |
| `429 Too many requests` | Add a delay and limit concurrent API calls. |
| Missing results | Check `next` and retrieve the remaining pages. |

The HTTP status codes are described in the [Rebrickable API documentation](https://rebrickable.com/api/v3/docs/).
