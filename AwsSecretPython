import os
import json
from flask import Flask, jsonify

# Check if running on AWS Lambda
IS_LAMBDA = os.getenv('AWS_LAMBDA_FUNCTION_NAME') is not None

app = Flask(__name__)

# Function to fetch secrets from AWS Secrets Manager
def get_secret_from_secrets_manager(secret_name):
    if IS_LAMBDA:
        import boto3
        client = boto3.client('secretsmanager')
        response = client.get_secret_value(SecretId=secret_name)
        secret = json.loads(response['SecretString'])
        return secret
    else:
        # For local development, read from a .toml file
        import toml
        with open('secrets.toml', 'r') as f:
            secrets = toml.load(f)
            return secrets.get(secret_name, {})

# Route to get a secret by name
@app.route('/secret/<secret_name>')
def get_secret(secret_name):
    secret = get_secret_from_secrets_manager(secret_name)
    return jsonify(secret)

if __name__ == '__main__':
    # Run the Flask app locally
    app.run(debug=True)
