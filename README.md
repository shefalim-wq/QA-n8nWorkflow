# Daily Top 5 Posts Digest --- n8n Workflow

## Overview

This workflow runs on an hourly schedule, retrieves posts from the
JSONPlaceholder demo API, selects the five posts with the highest IDs,
retrieves author information, formats a digest, and sends the digest to
Telegram. An error output from the author-information request is routed
to a separate Telegram alert node.

This is a learning/demo workflow. JSONPlaceholder provides sample data
and does not publish real news or live social-media posts.

## Workflow

1.  **Schedule Trigger** --- starts the workflow every hour.
2.  **HTTP Request** --- retrieves posts from
    `https://jsonplaceholder.typicode.com/posts`.
3.  **Code** --- sorts the posts by `id` in descending order and keeps
    the first five.
4.  **HTTP Request1** --- retrieves author information from the
    JSONPlaceholder users endpoint.
5.  **HTTP Request2** --- performs the author-information request used
    by the downstream workflow. Its **On Error** setting is
    `Continue (using error output)`.
6.  **IF** --- checks whether the returned author ID is greater than 5.
    In the sample run, the selected posts belong to user ID 10, so they
    follow the true branch.
7.  **Code in JavaScript1** --- combines the selected posts with the
    returned author details and creates one formatted digest.
8.  **Send a text message** --- sends the digest to Telegram.
9.  **Send a text message1** --- sends a Telegram alert when the second
    author-information request follows its error output.

## APIs used

-   Posts: `https://jsonplaceholder.typicode.com/posts`
-   Sample author endpoint:
    `https://jsonplaceholder.typicode.com/users/10`

The current exported workflow uses the fixed author endpoint ending in
`/10`. In the supplied sample data, the five highest-ID posts (100--96)
all have `userId` 10. For a more reusable workflow, change the author
request URL to use the incoming post's `userId` dynamically and remove
the redundant duplicate author lookup after validating the revised
workflow.

## Setup / Import

1.  In n8n, import `daily_top_5_posts_digest_sanitized.json`.
2.  Open both Telegram nodes and select or create your Telegram API
    credential.
3.  Replace `REPLACE_WITH_YOUR_TELEGRAM_CHAT_ID` in both Telegram nodes
    with your own Telegram chat ID.
4.  Save the workflow.
5.  Run **Execute workflow** and confirm that the digest arrives in
    Telegram.
6.  Keep the workflow inactive until you are ready to enable the hourly
    schedule.

## Credentials and security

-   The sanitized export intentionally omits Telegram credential
    references and replaces the Chat ID with a placeholder.
-   Add credentials inside n8n using its Credentials interface. Do not
    hardcode a bot token in node parameters or commit tokens, passwords,
    or private chat identifiers to a public repository.
-   If you share screenshots, hide tokens and personal identifiers.

## Error handling

The second author-information HTTP Request node is configured with **On
Error → Continue (using error output)**. Its error output is connected
to a separate Telegram node that sends a workflow error alert. The
normal output continues to the IF node and digest path.

## Evidence to include

Add your own screenshots to a `screenshots/` folder, for example:

-   `screenshots/workflow-canvas.png` --- complete workflow canvas.
-   `screenshots/successful-execution.png` --- successful execution
    showing the main nodes.
-   `screenshots/telegram-digest.png` --- received digest in Telegram.
-   `screenshots/error-alert.png` --- optional evidence of the
    error-alert path, captured only after testing it.

Do not claim the error-alert path was tested unless you have
deliberately tested it and captured the result.

## Known demo limitations

-   The workflow currently uses a fixed `/users/10` endpoint and
    includes two consecutive author HTTP Request nodes. This is adequate
    for the demonstrated sample output but is not the cleanest
    general-purpose implementation.
-   The IF condition checks the author ID, not the post ID.
-   The error branch is configured for the second author request only.
-   The workflow uses JSONPlaceholder sample content, not a production
    data source.

## Files

-   `daily_top_5_posts_digest_sanitized.json` --- sanitized n8n workflow
    export.
-   `README.md` --- setup and workflow documentation.
-   `screenshots/` --- execution evidence to be added.
