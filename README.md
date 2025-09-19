# Foundry VTT with Tailscale Setup

This repository provides a Docker-based setup for running Foundry Virtual Tabletop with Tailscale networking. It uses the [felddy-foundryvtt-docker](https://github.com/felddy/foundryvtt-docker) container and the [Tailscale](https://hub.docker.com/r/tailscale/tailscale) docker container. 
This is designed to work out of the box with a few prerequisites. 

## Prerequisites

- A functioning docker installation.
- A foundryvtt account with a purchased license.
- A tailscale account. 

## Configuration

### 1. Foundry Credentials

Create folder named secrets in your root directory, then a file named `foundry-secrets.json` in the root directory with your Foundry credentials:

```json
{
    "foundry_username": "your_username",
    "foundry_password": "your_password",
    "foundry_admin_key": "your_admin_key"
}
```

This file will be used to authenticate with Foundry and run the instance in your Docker container.

### 2. Tailscale Authentication

Create a `.env` file in the root directory containing your Tailscale auth key:

When creating the auth key, it should be set with a tag. Mine uses 'ttrpg' but you can configure yours however you would like. 

```env
TS_AUTHKEY=tskey-auth-***
```

To obtain your auth key:
1. Log into your [Tailscale admin console](https://login.tailscale.com/admin) or create an account if you haven't already.
2. Navigate to **Settings** > **Keys**
3. Generate a new auth key

### 3. Tailscale Configuration

Before running the container, configure the following in your Tailscale admin console:

#### Enable MagicDNS and HTTPS
1. Go to **DNS** settings
2. Enable **MagicDNS**
3. Enable **HTTPS certificates**

#### Set Tailnet Name (Optional)
You can customize your tailnet name in the **DNS** section. The full URL will use the hostname set in your docker-compose file and your tailnet name.

#### Configure Node Attributes
In **Access Controls** > **Node Attributes**, add your tag (replace `tag:ttrpg` with your preferred tag):

```json
"nodeAttrs": [
    {
        // Funnel policy, which lets tailnet members control Funnel
        // for their own devices.
        // Learn more at https://tailscale.com/kb/1223/tailscale-funnel/
        "target": ["autogroup:member", "tag:ttrpg"],
        "attr":   ["funnel"],
    },
],
```

#### Set Tag Owners
Also in **Access Controls**, define tag ownership:

```json
"tagOwners": {
    "tag:ttrpg": ["autogroup:member"]
}
```

## Running the Setup

Once configuration is complete, start the Docker container:

```bash
docker compose up --detach
```

To close your docker instance, simply run

```bash
docker compose down
```

## Accessing Your Game

It may take a few moments for the url to be accessible.

After the container starts:

1. Check your [Tailscale admin console](https://login.tailscale.com/admin/machines)
2. Locate your machine in the devices list
3. The Funnel feature should be visible with connection addresses
4. Use the provided URL in the dropdown list to access your Foundry VTT instance via https.

If everything was correct, happy gaming! 🎲

## Troubleshooting

- Ensure all configuration files are in the correct paths.
- Verify your Tailscale auth key has the necessary permissions and the proper tag.
- Check that MagicDNS and HTTPS are enabled in Tailscale settings.
- Confirm your tag is properly configured in Access Controls.
