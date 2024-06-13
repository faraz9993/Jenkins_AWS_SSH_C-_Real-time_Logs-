pipeline {
   agent any

   environment {
      SSH_KEY_ID = 'ec2-ssh-key' // The ID of the SSH key credentials in Jenkins
   }

   stages {
      stage('Connect to EC2 and Run Command') {
         steps {
            script {
               sshagent(credentials: [SSH_KEY_ID]) {
                  sh '''
                     ssh -o StrictHostKeyChecking=no ec2-user@3.92.169.188 <<'EOF'
                         echo "Connected to EC2 instance"
                         g++ -o /tmp/log_writer /path/to/log_writer.cpp &&
                         /tmp/log_writer &
                         while :; do
                             # Get the initial modification time of the log file
                             initial_mtime=$(stat -c %Y /tmp/sample_logs.log)
                             
                             # Monitor the log file for 10 seconds
                             timeout 10 tail -f /tmp/sample_logs.log
                             
                             # Get the modification time of the log file after monitoring
                             final_mtime=$(stat -c %Y /tmp/sample_logs.log)
                             
                             # Compare the modification times
                             if [ "$initial_mtime" -eq "$final_mtime" ]; then
                                 echo "No new logs for 10 seconds, exiting loop."
                                 exit 0
                             fi
                         done
                     EOF
                  '''
               }
            }
         }
      }
   }
}
