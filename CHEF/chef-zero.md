# Chef-Zero


The easiest setup is:

    gem install chef-zero chef-cli

Check the installation:

    chef-zero --version
    chef-client --version

If chef-client is unavailable, install Cinc Client instead:


    gem install cinc-client
    cinc-client --version




### Create a minimal cookbook
This example targets Ubuntu/Debian:

    mkdir -p ~/chef-nginx/cookbooks/nginx/recipes
    cd ~/chef-nginx

Create the recipe:


    service 'nginx' do
      action [:enable, :start]
    end
    
    file '/var/www/html/index.html' do
      content 'Hello from Chef Zero!'
      owner 'root'
      group 'root'
      mode '0644'
    end
    EOF
    


Create the Chef configuration:

    cat > solo.rb <<'EOF'
    cookbook_path [File.expand_path('cookbooks', __dir__)]
    EOF


### Run the recipe
Use Chef Client local mode. It automatically starts a temporary Chef Zero server:

    sudo chef-client --local-mode \
      --config-option cookbook_path="$PWD/cookbooks" \
      --runlist 'recipe[nginx]'


Or, with Cinc:


    sudo cinc-client --local-mode \
      --config-option cookbook_path="$PWD/cookbooks" \
      --runlist 'recipe[nginx]'


A cleaner version using solo.rb is:

    
    sudo chef-client --local-mode \
      --config solo.rb \
      --runlist 'recipe[nginx]'

  



The command should install and start Nginx. Verify it:

    systemctl status nginx
    curl http://localhost

You should see:

    Hello from Chef Zero!



### Run Chef Zero explicitly
You can start Chef Zero yourself:

   
    cd ~/chef-nginx
    chef-zero -r .

It will print a URL such as:

    chefzero://localhost:1

However, for local cookbook development, you usually do not need to start it separately. chef-client --local-mode starts and uses Chef Zero automatically.


Important notes
Run the convergence with sudo because installing packages and managing services require root privileges.
The recipe is idempotent: running it again should report that most resources are already up to date.
To remove Nginx later, change the resources to action :remove and action :stop, or uninstall it directly:


    sudo apt remove nginx

For a production-like workflow, use a full Chef/Cinc server or Test Kitchen; Chef Zero is primarily intended for local development and testing
