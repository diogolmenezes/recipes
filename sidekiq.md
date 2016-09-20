# Sidekiq

- https://github.com/mperham/sidekiq
- https://github.com/mperham/sidekiq/wiki/Active-Job
- https://gist.github.com/maxivak/690e6c353f65a86a4af9
- https://github.com/mperham/sidekiq/wiki/Monitoring


# Instalar o Redis

    sudo apt-get install redis-server

# Incluir a Gem

    gem 'sidekiq'
    gem 'sinatra', :require => nil

# Configurar o active job para usar o sidekiq

    Dentro de config/initializers criar o arquivo active_job.rb

    ```if Rails.env.test? || Rails.env.development?
      ActiveJob::Base.queue_adapter = :inline
    else
      ActiveJob::Base.queue_adapter = :sidekiq
    end```

# Configurar a rota da interface no routes.rb

    ```
    #sidekiq
      require "sidekiq/web"
      Sidekiq::Web.use Rack::Auth::Basic do |username, password|
        username == 'username' && password == 'password'
      end if Rails.env.production?
      mount Sidekiq::Web, at: "/sidekiq"
    ```

# Colocar no upstart o comando de inicialização do sidekiq

    bundle exec sidekiq -q default -q mailers