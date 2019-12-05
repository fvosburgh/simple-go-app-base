podTemplate(yaml: """
kind: Pod
metadata:
  name: kaniko-yaml
spec:
  containers:
  - name: kaniko-yaml
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: jenkins-docker-cfg
        mountPath: /kaniko/.docker
  volumes:
  - name: jenkins-docker-cfg
    projected:
      sources:
      - secret:
          name: harbor-credentials
          items:
            - key: config.json
              path: config.json
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
          /kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --destination=poc-dtr-1.boxboat.net/admin/demo/go-app-base
          '''
        }
      }
    }
  }
}
