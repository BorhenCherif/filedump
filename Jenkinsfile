#!groovy
node ('worker_node1') {
   properties([disableConcurrentBuilds()])
   sleep 30
   // always run with a new workspace
   step([$class: 'WsCleanup'])
   try {
      stage('Source') {
	 checkout scm
	 stash name: 'test-sources', includes: 'build.gradle,src/test/'
      }
      stage('Build') {
         // Run the gradle build
         gbuild4 'clean build -x test'
      }
      stage ('Test') {
      // execute required unit tests in parallel
      
         parallel (
            master: { node ('master'){
               // always run with a new workspace
               step([$class: 'WsCleanup'])
	       unstash 'test-sources'		
	       gbuild4 '-D test.single=TestExample1 test'
            }},
            worker2: { node ('worker_node2'){
               // always run with a new workspace
               step([$class: 'WsCleanup'])
               unstash 'test-sources'
  	       gbuild4 '-D test.single=TestExample2 test'
            }},
         ) 
      } 
   } 
   catch (err) {
      echo "Caught: ${err}"
   }
   stage ('Notify') {
     // mailUser('<your email address>', "Finished")
   }
   
}

