podTemplate(yaml: """
kind: Pod
metadata:
  name: kaniko-yaml
spec:
  containers:
  - name: kaniko-yaml
    image: gcr.io/kaniko-project/executor:debug-539ddefcae3fd6b411a95982a830d987f4214251
    imagePullPolicy: Always
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: jenkins-docker-cfg
        mountPath: /root
  volumes:
  - name: jenkins-docker-cfg
    projected:
      sources:
      - secret:
          name: harbor-credentials
          items:
            - key: config.json
              path: .docker/config.json
"""
  ) {
  node(POD_LABEL) {
    stage('checkout') {
      checkout scm
    }
    stage('build') {
      container(name: 'kaniko-yaml', shell: '/busybox/sh') {
        withEnv(['PATH+EXTRA=/busybox:/kaniko']) {
          sh '''#!/busybox/sh
          /kaniko/executor -f `pwd`/Dockerfile -c `pwd` /kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --destination=harbor.azure.training.boxboat.io/library/go-app-base
          '''
        }
      }
    }
    stage('build downstream'){
      build job: 'simple-go-app/master'
    }
  }
}
