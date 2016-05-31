# dockered_jenkins

# Ephemeral Build Slaves using Jenkins and Docker

This repo all the files necessary to create your own local Docker-Toolbox version of a fully ephemeral jenkins environment.  Build slaves are designed to
be docker containers in this configuration. The only thing missing to make it work is your Docker Machine certs (see directions below on how to add these).

# Quick Start

1. Make sure you have all the pre-reqs installed (i.e. Docker-toolbox) Ubuntu install is here https://docs.docker.com/engine/installation/linux/ubuntulinux/
2. Clone this repository to your local drive
3. Find the docker-machine folder container your 4 client pem keys (ca-key.pem, ca.pem, key.pem, cert.pem). Mine for example, is in ~/.docker/machine/certs.
4. cp -R yourdockermachinecertsfolder jenkins-master/certs
5. make build
6. make run
7. Point your browser to http://yourdockermachineip
8. In jenkins, configure a ssh-key pair using an ssh private key for the user "jenkins". As the private key, use the file in jenkins-slave/files/dummy\_private\_rsa\_key
9. In jenkins, configure a Docker Certificates Directory credential using /usr/local/etc/jenkins/certs as the source directory
10. In jenkins configuration, add a new Docker cloud provider
  1. Set host to https://yourdockermachineip:2376
  2. Select the docker certificates directory you made
  3. Set read timeout to 5
  4. set connection timeout to 15
  5. Click on "Test Connection" and make sure you get a valid response
11. In jenkins config on your new Docker cloud add a Docker template
  1. Set the image name to: jenkins\_slave
  2. Create a label "testslave"
  3. Make sure credentials are your new ssh key pair you made above
  4. Click "Save"
12. Create a new free-style jenkins job
  1. Restrict the job to the label "testslave"
  2. Add a build step, like execute shell "echo 'Hello World!'"
  3. Save job
  4. Run job

If everything is configured correctly your job should dynamically allocate a slave, run itself and then de-allocate the slave.

# Jenkins-slave

 A docker image to be used for Jenkins testing

 This image provides
 * Languages
    * JDK 1.7, 1.8 / Maven 3.0.5
    * Node 0.10.24 via nvm
    * Ruby 2.1.2 via RVM
 * Services
    * Postgres 9.3
    * Xvfb
 * Browsers
     * Firefox
     * Chrome
     * Chromium

### Environment Variables

* `JAVA_OPTS`, Default: `-Xmx2G -Xms2G -XX:PermSize=256M -XX:MaxPermSize=256m`
* `DISPLAY`, Default `:10`
* `XVFB_SCREEN_SIZE`, Default `1024x768x24`

### Examples

> `BUILD_TAG` and `WORKSPACE` are jenkins environment variables.
