sudo apt update
sudo apt install openjdk-11-jdk
sudo apt install wget
wget -q -O - https://pkg.jenkins.io/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian/ stable main > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
