# Terraform AWS Lambda Shell (Data)

This module runs shell commands in a Lambda function and treats the result like a data source (re-runs during each refresh/plan step). It is intended to be used with the [Invicton-Labs/lambda-shell/aws](https://registry.terraform.io/modules/Invicton-Labs/lambda-shell/aws/latest) module.

## Usage

`main.tf`
```
module "lambda-shell" {
  source = "Invicton-Labs/lambda-shell/aws"

  // Give the Lambda function the ability to run any request.
  // You can restrict this to whichever permissions you actually need.
  lambda_role_policy_arns = [
    "arn:aws:iam::aws:policy/AdministratorAccess"
  ]
}

module "lambda-shell-data" {
  source              = "Invicton-Labs/lambda-shell-data/aws"

  // Pass in the Lambda Shell module
  lambda_shell_module = module.lambda-shell

  // Run the command using the Python interpreter
  interpreter   = ["python3"]
  // Load the command/script from a file
  command       = file("describe-regions.py")
  // Cause Terraform to fail if the function throws an error
  fail_on_error = true
}

output "shell-data" {
  value = module.lambda-shell-data
}
```

`describe-regions.py`
```
import boto3

ec2 = boto3.client('ec2')

if __name__ == "__main__":
    # Retrieves all regions/endpoints that work with EC2
    response = ec2.describe_regions()
    print('Regions:', response['Regions'])
```

Output:
```
Outputs:

shell-data = {
  "exitstatus" = 0
  "stderr" = ""
  "stdout" = <<-EOT
  Regions: [{'Endpoint': 'ec2.eu-north-1.amazonaws.com', 'RegionName': 'eu-north-1', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.ap-south-1.amazonaws.com', 'RegionName': 'ap-south-1', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.eu-west-3.amazonaws.com', 'RegionName': 'eu-west-3', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.eu-west-2.amazonaws.com', 'RegionName': 'eu-west-2', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.eu-west-1.amazonaws.com', 'RegionName': 'eu-west-1', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.ap-northeast-3.amazonaws.com', 'RegionName': 'ap-northeast-3', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.ap-northeast-2.amazonaws.com', 'RegionName': 'ap-northeast-2', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.ap-northeast-1.amazonaws.com', 'RegionName': 'ap-northeast-1', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.sa-east-1.amazonaws.com', 'RegionName': 'sa-east-1', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.ca-central-1.amazonaws.com', 'RegionName': 'ca-central-1', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.ap-southeast-1.amazonaws.com', 'RegionName': 'ap-southeast-1', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.ap-southeast-2.amazonaws.com', 'RegionName': 'ap-southeast-2', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.eu-central-1.amazonaws.com', 'RegionName': 'eu-central-1', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.us-east-1.amazonaws.com', 'RegionName': 'us-east-1', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.us-east-2.amazonaws.com', 'RegionName': 'us-east-2', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.us-west-1.amazonaws.com', 'RegionName': 'us-west-1', 'OptInStatus': 'opt-in-not-required'}, {'Endpoint': 'ec2.us-west-2.amazonaws.com', 'RegionName': 'us-west-2', 'OptInStatus': 'opt-in-not-required'}]

  EOT
}
```