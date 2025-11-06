import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=<c1448ad3-82ee-4b78-8c4d-7d4c16710b82>',
        'AZURE_TENANT_ID=<eff4771c-2b8c-4519-9806-ce43df84350e>']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      node {
  withEnv([
    'AZURE_SUBSCRIPTION_ID=<your_subscription_id>',
    'AZURE_TENANT_ID=<your_tenant_id>'
  ]) {

    stage('build') {
      sh 'mvn clean package'
    }

    stage('deploy') {
      withCredentials([usernamePassword(
        credentialsId: 'AzureServicePrincipal',
        usernameVariable: 'AZURE_CLIENT_ID',
        passwordVariable: 'AZURE_CLIENT_SECRET')]) {

        sh '''
/usr/bin/az login --service-principal \
  -u "$AZURE_CLIENT_ID" -p "$AZURE_CLIENT_SECRET" -t "$AZURE_TENANT_ID"
/usr/bin/az account set --subscription "$AZURE_SUBSCRIPTION_ID"
/usr/bin/az webapp deploy \
  --resource-group jenkins-get-started-rg \
  --name <yourappname> \
  --src-path target/calculator-1.0.war --type war
'''
      }
    }
  }
}
}
