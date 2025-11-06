pipeline {
  agent any

  environment {
    AZURE_SUBSCRIPTION_ID = 'c1448ad3-82ee-4b78-8c4d-7d4c16710b82'
    AZURE_TENANT_ID       = 'eff4771c-2b8c-4519-9806-ce43df84350e'
    RESOURCE_GROUP        = 'jenkins-get-started-rg'
    WEBAPP_NAME           = 'jenkins-get-started-app-baozhi'
  }

  stages {
    stage('build') {
      steps {
        sh 'mvn clean package'
      }
    }

    stage('deploy') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'AzureServicePrincipal',
          usernameVariable: 'AZURE_CLIENT_ID',
          passwordVariable:  'AZURE_CLIENT_SECRET'
        )]) {
          sh '''/usr/bin/az login --service-principal \
  -u "$AZURE_CLIENT_ID" -p "$AZURE_CLIENT_SECRET" -t "$AZURE_TENANT_ID"
/usr/bin/az account set --subscription "$AZURE_SUBSCRIPTION_ID"
/usr/bin/az webapp deploy \
  --resource-group "$RESOURCE_GROUP" \
  --name "$WEBAPP_NAME" \
  --src-path target/calculator-1.0.war --type war
/usr/bin/az logout
'''
        }
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'target/*.war', fingerprint: true
    }
  }
}
