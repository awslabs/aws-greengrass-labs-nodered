## Building and publishing

This component does not require any artifact and can be published via the AWS CLI.

Pre-requisite:
1. [AWS CLI v2](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
1. IAM credentials with sufficient permissions to do `"greengrass:CreateComponentVersion"`

To publish the component in your account, execute:

```bash
aws greengrassv2 create-component-version --inline-recipe fileb://./recipe.yaml
```

If you have [gdk](https://github.com/aws-greengrass/aws-greengrass-gdk-cli) installed, you can also use
the following commands:


```bash
gdk component build
gdk component publish
```
