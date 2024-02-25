#!/usr/bin/env groovy
def label = "power-bi-trino-connector-${UUID.randomUUID().toString()}"
podTemplate(label: label, yaml: """
    apiVersion: v1
    kind: Pod
    metadata:
        name: ${label}
    labels:
        app: ${label}
    spec:
      containers:
      - name: cct-datascience-base
        image: cityofcapetown/datascience:base
        imagePullPolicy: IfNotPresent
        command:
        - cat
        tty: true
      nodeSelector:
        workload: batch
    """) {
    node(label) {
        stage('power-bi-trino-connector setup') {
            git credentialsId: 'jenkins-user', url: 'https://ds1.capetown.gov.za/ds_gitlab/ginggs/powerbitrinoconnector.git'
            updateGitlabCommitStatus name: 'setup', state: 'success'
        }
        stage('power-bi-trino-connector build') {
            withCredentials([usernamePassword(credentialsId: 'power-bi-trino-connector-secret, passwordVariable: 'TRINO_OAUTH_SECRET_ID', usernameVariable: 'TRINO_OAUTH_CLIENT_ID')]) {
                container('cct-datascience-base') {
                        sh label: 'sub_script', script: '''#!/usr/bin/env bash
                            sed "s/<OAUTH_CLIENT_ID_GOES_HERE>/"${TRINO_OAUTH_CLIENT_ID}"/" ./Trino.pq.tmpl > /tmp/Trino.pq
                            sed -i -e "s/<OAUTH_SECRET_ID_GOES_HERE>/"${TRINO_OAUTH_SECRET_ID}"/" /tmp/Trino.pq
                        exit $?'''
                }
            }
            updateGitlabCommitStatus name: 'build', state: 'success'
        }
        stage('power-bi-trino-connector upload') {
            container('cct-datascience-base') {
                withCredentials([usernamePassword(credentialsId: 'minio-lake-credentials', passwordVariable: 'MINIO_SECRET', usernameVariable: 'MINIO_ACCESS')]) {
                    sh label: 'upload_script', script: '''#!/usr/bin/env bash
                        file=/tmp/Trino.pq
                        filename=Trino.pq
                        bucket=power-bi-custom-connector
                        resource="/${bucket}/${filename}"
                        contentType="application/octet-stream"
                        dateValue=`date -R`
                        stringToSign="PUT\\n\\n${contentType}\\n${dateValue}\\n${resource}"
                        signature=$(echo -en ${stringToSign} | openssl sha1 -hmac ${MINIO_SECRET} -binary | base64)
                        curl -v --fail \\
                          -X PUT -T "${file}" \\
                          -H "Host: lake.capetown.gov.za" \\
                          -H "Date: ${dateValue}" \\
                          -H "Content-Type: ${contentType}" \\
                          -H "Authorization: AWS ${MINIO_ACCESS}:${signature}" \\
                          https://lake.capetown.gov.za/${resource}
                        exit $?'''
                }
            }
            updateGitlabCommitStatus name: 'upload', state: 'success'
        }
    }
}