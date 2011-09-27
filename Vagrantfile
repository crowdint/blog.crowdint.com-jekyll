Vagrant::Config.run do |config|
  config.vm.box = "lucid64"
  config.vm.box_url = "http://files.vagrantup.com/lucid64.box"
  config.vm.network "33.33.33.10"

  # config.vm.boot_mode = :gui

  config.vm.forward_port "jekyll"       , 4000, 4000
  # config.vm.forward_port "mongodb_admin" , 28017 , 28017
  # config.vm.forward_port "mysql"         , 3306  , 3306
  # config.vm.forward_port "postgresql"    , 5432  , 5432
  # config.vm.forward_port "solr"          , 8983  , 8983
  # config.vm.forward_port "solr_test"     , 8984  , 8984

  config.vm.provision :chef_solo do |chef|
     chef.cookbooks_path = "cookbooks"

     chef.add_recipe "general"
     chef.add_recipe "general::utils"
     # chef.add_recipe "mongodb"
     # chef.add_recipe "mysql"
     # chef.add_recipe "postgresql"
     # chef.add_recipe "redis"
     # chef.add_recipe "solr"
  end

  config.vm.customize do |vm|
    vm.memory_size = 1024
  end

  config.vm.share_folder "blog", "./blog", ".",
      {:nfs => true}
end
