Vagrant.configure('2') do |config|
  config.vm.box = 'ubuntu/trusty64'

	N = 3
	(1..N).each do |machine_id|
		config.vm.define "rabbitmq#{machine_id}" do |machine|
      machine.vm.hostname = "rabbitmq#{machine_id}.local"
			machine.vm.network "private_network", ip: "192.168.77.#{20+machine_id}"

      config.vm.provision 'shell',
        inline: 'sudo apt-get install -y python-minimal'

			if machine_id == N then
        config.vm.provision :ansible,
          playbook: 'playbook.yml',
          groups: { rabbitmq: (1..N).map { |n| "rabbitmq#{n}" }},
          raw_arguments: %w(-vv --diff),
          limit: :all
			end
		end
	end
end
