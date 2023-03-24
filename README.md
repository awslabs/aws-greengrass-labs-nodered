# aws.greengrass.labs.nodered

This component deploys [Node-RED](https://nodered.org/) onto a Greengrass device. The Node-Red environment has access to all the environment variable resources set by Greengrass which allows to interact with Greengrass services and other component such as TokenExchangeServer and StreamManager.

To use other components, such as [Stream manager](https://docs.aws.amazon.com/greengrass/v2/developerguide/stream-manager-component.html) or any of the [Machine learning components](https://docs.aws.amazon.com/greengrass/v2/developerguide/machine-learning-components.html) you can add them to the deployment. 

This component installs a specific version of Node-RED and for consistency the component version tracks the Node-RED version being deployed.

To remotely deploy flows to Greengrass Core devices running this component you can use the [Greengrass CLI for Node-RED](https://github.com/awslabs/aws-greengrass-labs-node-red-app-cli).

## Versions
This component has the following versions:

* 3.0.2

## Type

This component is a generic component.

For more information, see [component types](https://docs.aws.amazon.com/greengrass/v2/developerguide/manage-components.html#component-types)

## Deploy to Greengrass Core

To deploy this component to a device you need to also add the following components to your account:
- [aws.greengrass.labs.nodered.auth](https://github.com/awslabs/aws-greengrass-labs-nodered-auth)
- [aws.greengrass.labs.SecrectsManagerClient](https://github.com/awslabs/aws-greengrass-labs-SecretsManagerClient)


### Authentication
You can configure authentication for the access to the Node-RED UI via the `aws.greengrass.labs.nodered.auth` component. By default authentication is not enabled (`"username": "none"`). 

To configure authentication, set the `"username"` value in the component configuration to the chosen user name, and create a custom secret in SecretsManager called `aws.greengrass.labs.nodered/<username>` and with a text value corresponding to the chosen password.

You also need to add the following permissions to the token exchange service role associated with Greengrass, taking care of replacing the placeholders with the actual values:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "secretsmanager:GetSecretValue",
            "Resource": [
                "arn:aws:secretsmanager:<region>:<accountid>:secret:aws.greengrass.labs.nodered/<secretid>"
            ]
        }
    ]
}
```

## Access the Node-RED UI

The Node-RED web UI is only accessible on localhost. To be able to access it remotely one can connect to the device via an SSH tunnel and forward the UI port (`1880`) to the machine from which the connection is established. Then open the url http://localhost:1880 in a browser.

To establish the ssh tunnel:

```bash
ssh -L 1880:localhost:1880 user@host
```

### Greengrass Core behind a firewall

If the device is not directly accessible, for example because behind a firewall or a home router, it is possible to use [AWS System Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-instances-and-nodes.html) to establish a port forwarding tunnel. 

1. Deploy the [aws.greengrass.SystemsManagerAgent](https://console.aws.amazon.com/iot/home#/greengrass/v2/components/public/aws.greengrass.SystemsManagerAgent) to the Greengrass Core device
2. Install the [AWS Systems Manager CLI extension](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-install-plugin.html)
3. Establish a tunnel with the device using:
    ```bash
    export INSTANCE_ID=<instanceId from SSM>
    aws ssm start-session --target $INSTANCE_ID \
                    --document-name AWS-StartPortForwardingSession \
                    --parameters '{"portNumber":["1880"],"localPortNumber":["1880"]}'
    ```



## Requirements

This component requires [Node](https://nodejs.org/en/) v14 or above to be present on the device. 

This component allows the user to write custom code at runtime and additional requirements in term of ports used or permissions cannot be pre-defined.

## Dependencies

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component. This section lists the dependencies for the released versions of this component and the semantic version constraints that define the component versions for each dependency. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass). On the component details page, look for the Dependencies list.

### 3.0.2

| Dependency | Compatible versions | Dependency type |
|---|---|---|
| Token exchange service | >=0.0.0 | Hard |
| [aws.greengrass.labs.nodered.auth](https://github.com/awslabs/aws-greengrass-labs-nodered-auth) | ~1.0.0 | Hard |

### 2.1.3

| Dependency | Compatible versions | Dependency type |
|---|---|---|
| Token exchange service | >=0.0.0 | Hard |
| [aws.greengrass.labs.nodered.auth](https://github.com/awslabs/aws-greengrass-labs-nodered-auth) | ~1.0.0 | Hard |

Check [aws.greengrass.labs.nodered.auth](https://github.com/awslabs/aws-greengrass-labs-nodered-auth) for additional components that might need to be configured.

## Configuration

This component provides the following configuration parameters that you can customize when you deploy the component.

`headless`

Configure Node-RED to run in headless mode. Is set to `true`, no UI will be available.


## Local log file

This component uses the following log file:

```bash
/greengrass/v2/logs/aws.greengrass.labs.nodered.log
```


## Changelog

The following table describes the changes in each version of the component.

| Version | Changes |
|---|---|
| 3.0.2 | Initial version for Node-RED 3.0.2 |
| 2.1.3 | Initial version for Node-RED 2.1.3 |


## Thank you

This component would have not been possible without the great work done by Node-RED at [nodered.org](https://nodered.org/)