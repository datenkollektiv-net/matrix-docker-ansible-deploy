# Setting up Appservice Webhooks bridging (optional, deprecated)

**Note**: This bridge has been deprecated. We recommend not bothering with installing it. While not a 1:1 replacement, the bridge's author suggests taking a look at [matrix-hookshot](https://github.com/matrix-org/matrix-hookshot) as a replacement, which can also be installed using [this playbook](configuring-playbook-bridge-hookshot.md). Consider using that bridge instead of this one.

The playbook can install and configure [matrix-appservice-webhooks](https://github.com/turt2live/matrix-appservice-webhooks) for you. This bridge provides support for Slack-compatible webhooks.

Setup Instructions:

loosely based on [this](https://github.com/turt2live/matrix-appservice-webhooks/blob/master/README.md)

1. Add the following configuration to your `inventory/host_vars/matrix.example.com/vars.yml` file:

    ```yaml
    matrix_appservice_webhooks_enabled: true
    matrix_appservice_webhooks_api_secret: '<your_secret>'
    ```

2. In case you want to change the verbosity of logging via `journalctl -fu matrix-appservice-webhooks.service` you can adjust this in `inventory/host_vars/matrix.example.com/vars.yml` as well.

    **Note**: default value is: `info` and availabe log levels are : `info`, `verbose`

    ```yaml
    matrix_appservice_webhooks_log_level: '<log_level>'
    ```

3. As of Synapse 1.90.0, you will need to add the following to `matrix_synapse_configuration_extension_yaml` to enable the [backwards compatibility](https://matrix-org.github.io/synapse/latest/upgrade#upgrading-to-v1900) that this bridge needs:

    ```yaml
    matrix_synapse_configuration_extension_yaml: |
      use_appservice_legacy_authorization: true
    ```

    **Note**: This deprecated method is considered insecure.

4. If you've already installed Matrix services using the playbook before, you'll need to re-run it (`--tags=setup-all,start`). If not, proceed with [configuring other playbook services](configuring-playbook.md) and then with [Installing](installing.md). Get back to this guide once ready.

5. If you're using the [Dimension integration manager](configuring-playbook-dimension.md), you can configure the Webhooks bridge by opening the Dimension integration manager -> Settings -> Bridges and selecting edit action for "Webhook Bridge". Press "Add self-hosted Bridge" button and populate "Provisioning URL"  & "Shared Secret" values from `/matrix/appservice-webhooks/config/config.yaml` file's homeserver URL value and provisioning secret value, respectively.

6. Invite the bridge bot user to your room:

    - either with `/invite @_webhook:example.com` (**Note**: Make sure you have administration permissions in your room)

    - or simply add the bridge bot to a private channel (personal channels imply you being an administrator)

7. Send a message to the bridge bot in order to receive a private message including the webhook link.

    ```
    !webhook
    ```

8. The JSON body for posting messages will have to look like this:

    ```json
    {
        "text": "Hello world!",
        "format": "plain",
        "displayName": "My Cool Webhook",
        "avatar_url": "http://i.imgur.com/IDOBtEJ.png"
    }
    ```

    You can test this via curl like so:

    ```sh
    curl --header "Content-Type: application/json" \
    --data '{
    "text": "Hello world!",
    "format": "plain",
    "displayName": "My Cool Webhook",
    "avatar_url": "http://i.imgur.com/IDOBtEJ.png"
    }' \
    <the link you've gotten in 5.>
    ```
