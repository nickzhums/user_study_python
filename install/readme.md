## 1. Create Dev enviroment for Preview Version of Python SDK
run `install_env_track2.sh`(in linux)   
or you can install these packages from `packages` by yourself

## 2. create env values
We need to store app registration information as environment variables.   
For example in linux:
```bash
export AZURE_TENANT_ID=""
export AZURE_CLIENT_ID=""
export AZURE_CLIENT_SECRET=""
export AZURE_SUBSCRIPTION_ID=""
```
About how to create app registration, see here: https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app
