# Sidekiq

- https://github.com/mperham/sidekiq
- https://github.com/mperham/sidekiq/wiki/Active-Job
- https://gist.github.com/maxivak/690e6c353f65a86a4af9
- https://github.com/mperham/sidekiq/wiki/Monitoring


# Instalar o Redis

sudo apt-get install redis-server

# Colocar no upstart o comando de inicialização do sidekiq

bundle exec sidekiq -q default -q mailers