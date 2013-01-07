require 'berkshelf/vagrant'

Vagrant::Config.run do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # The path to the Berksfile to use with Vagrant Berkshelf
  # config.berkshelf.berksfile_path = "./Berksfile"

  # An array of symbols representing groups of cookbook described in the Vagrantfile
  # to skip installing and copying to Vagrant's shelf.
  # config.berkshelf.only = []

  # An array of symbols representing groups of cookbook described in the Vagrantfile
  # to skip installing and copying to Vagrant's shelf.
  # config.berkshelf.except = []

  config.vm.host_name = "zenoss"

  config.vm.box = "CentOS-6.3-x86_64-minimal"

  config.vm.box_url = "https://www.dropbox.com/s/3alreuqfjt2qqog/CentOS-6.3-x86_64-minimal.box\?dl=1"

  # Boot with a GUI so you can see the screen. (Default is headless)
  # config.vm.boot_mode = :gui

  # Assign this VM to a host-only network IP, allowing you to access it
  # via the IP. Host-only networks can talk to the host machine as well as
  # any other machines on the same network, but cannot be accessed (through this
  # network interface) by any external networks.
  #config.vm.network :hostonly, "33.33.33.11"

  # Assign this VM to a bridged network, allowing you to connect directly to a
  # network using the host's network device. This makes the VM appear as another
  # physical device on your network.

  # config.vm.network :bridged

  # Forward a port from the guest to the host, which allows for outside
  # computers to access the VM, whereas host only networking does not.
  config.vm.forward_port 8080, 8082

  # Share an additional folder to the guest VM. The first argument is
  # an identifier, the second is the path on the guest to mount the
  # folder, and the third is the path on the host to the actual folder.
  # config.vm.share_folder "v-data", "/vagrant_data", "../data"

  config.vm.customize ["modifyvm", :id, "--memory", '8192']

  config.ssh.max_tries = 40
  config.ssh.timeout   = 120

  config.vm.provision :chef_solo do |chef|
    chef.json = {
      :mysql => {
        :server_root_password => 'rootpass',
        :server_repl_password => 'replpass'
      },
      :zends => {
        :server_root_password => 'rootpass',
        :server_repl_password => 'replpass',
        :centos6 => {
           :x86_64 => {
             :url => "http://artifacts.zenoss.loc/releases/4.2.3/1695/zends/zends-5.5.25a-1.r64630.el6.x86_64.rpm"
           }
        }
      },
      "rabbitmq" => {
           "url" => "http://deps.zenoss.com/yum/4.2.x/centos/6/os/x86_64/rabbitmq-server-2.8.6-1.noarch.rpm"
      },
      'maven' => {
           '3' => {
               'url' => 'http://apache.cs.utah.edu/maven/maven-3/3.0.4/binaries/apache-maven-3.0.4-bin.tar.gz'
           }
      },
      'yum' => {
          'epel_release' => '6-8'
      },
      'java' => {
          'install_flavor' => "oracle",
          'java_home' => '/usr/java/default/',
          "jdk" => {
              "6" => {
                  "x86_64" => {
                    "url" => "http://mac.edgar.loc:8080/jdk-6u35-linux-x64-rpm.bin", #file from a localwebserver
                    "checksum" => "a31198860f9f718077a746adf5ee48479b3b830d376b425c39b784087156b5a1", # sha256 checksum
                  }
              }
          },
      },
      "RM" => {
           'ZODBADMINPASSWORD' => "Zenoss1",
           'ZEPADMINPASSWORD' => "Zenoss1",
           "zenoss_src" => {
             "svn_username" => "USERNAME",
             "svn_password" => "PASSWORD",
           },
      },
    }

    chef.run_list = [
      "recipe[erlang]",
      "recipe[rabbitmq]",
      "recipe[iptables::disabled]",
      "recipe[openssh]",
      "recipe[zends::server]",
      "recipe[ZenossEnterprise::RMSource]"
    ]
  end
end
