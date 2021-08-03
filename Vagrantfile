# -*- mode: ruby -*-
# vi: set ft=ruby :
require 'yaml'

VAGRANTFILE_API_VERSION = "2"

options = File.exist?('vagrant.yml') ? YAML.load_file('vagrant.yml') : Hash.new

# default values
if !options['cpus']
  options['cpus'] = 2
end
if !options['cpucap']
  options['cpucap'] = "70"
end
if (!options['ram'])
  options['ram'] = "4096"
end
if (!options['ip'])
  options['ip'] = "10.0.0.100"
end
if (options['defaultShares'].nil?)
  options['defaultShares'] = true
end
if (!options['haleDownload'])
  options['haleDownload'] = "https://github.com/halestudio/hale/releases/download/4.0.0/hale-studio-4.0.0-linux.gtk.x86_64.tar.gz"
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "tranphuquy19/ubuntu-20.04-desktop"
  
  config.vm.provider "virtualbox" do |v|
    v.gui = true
    v.cpus = options['cpus']
    v.customize ["modifyvm", :id, "--cpuexecutioncap", options['cpucap']]
    v.customize ["modifyvm", :id, "--memory", options['ram']]
    
    v.customize ["setextradata", "global", "GUI/MaxGuestResolution", "any"]
    v.customize ["setextradata", :id, "CustomVideoMode1", "1024x768x32"]
    v.customize ["modifyvm", :id, "--graphicscontroller", "vmsvga"]
    v.customize ["modifyvm", :id, "--vram", "128"]
    if Vagrant::Util::Platform.windows?
      v.customize ["modifyvm", :id, "--audio", "dsound"]
    elsif Vagrant::Util::Platform.darwin?
      v.customize ["modifyvm", :id, "--audio", "coreaudio"]
    else
      v.customize ["modifyvm", :id, "--audio", "pulse"]
    end
    v.customize ["modifyvm", :id, "--audiocontroller", "ac97"]
    v.customize ["modifyvm", :id, "--audioout", "on"]
    v.customize ["modifyvm", :id, "--ioapic", "on"]
    v.customize ["modifyvm", :id, "--rtcuseutc", "on"]
    v.customize ["modifyvm", :id, "--accelerate3d", "off"]
    v.customize ["modifyvm", :id, "--nested-hw-virt", "on"]
    v.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
  end

  config.vm.network :private_network, ip: options['ip']

  # Provisioning via shell
  config.vm.provision "shell", privileged: true, inline: <<-EOF
    # delete scripts from base image
    rm /home/vagrant/Desktop/*.sh || true

    # download hale studio
    echo "Downloading hale studio from #{ options['haleDownload'] }"
    curl -L -o /home/vagrant/Downloads/hale.tar.gz #{ options['haleDownload'] }

    # extract
    rm -r /home/vagrant/apps/hale || true
    mkdir -p /home/vagrant/apps/hale
    tar -xf /home/vagrant/Downloads/hale.tar.gz --directory /home/vagrant/apps/hale
    mv /home/vagrant/apps/hale/*/* /home/vagrant/apps/hale/

    chown -R vagrant:vagrant /home/vagrant/apps/hale

    cat << EOS > /home/vagrant/Desktop/hale.desktop
    [Desktop Entry]
    Encoding=UTF-8
    Version=1.0
    Name=hale studio
    Comment=Starts hale studio
    Type=Application
    Exec=/home/vagrant/apps/hale/HALE
    Icon=/home/vagrant/apps/hale/icon.xpm
EOS

    chown vagrant:vagrant /home/vagrant/Desktop/hale.desktop
    chmod a+x /home/vagrant/Desktop/hale.desktop

    # allow launching
    # see https://stackoverflow.com/a/62240478/982265
    su vagrant -c "dbus-launch gio set /home/vagrant/Desktop/hale.desktop \"metadata::trusted\" true"
    su vagrant -c "dbus-send --type=method_call --print-reply --dest=org.gnome.Shell /org/gnome/Shell org.gnome.Shell.Eval string:'global.reexec_self()'"

    echo "Installation complete"
  EOF

  # Mounted folders
  # see https://docs.vagrantup.com/v2/synced-folders/basic_usage.html

  if options['defaultShares']
    if Vagrant::Util::Platform.windows?
      config.vm.synced_folder "C:\\Users", "/c/Users"
    end
  end

  # configured shares
  shares = options['shares']
  if !shares.nil?
    shares.each do |share|
      config.vm.synced_folder share['host'], share['vm']
    end
  end

end
