require 'pacto'

task :generate do
    WebMock.allow_net_connect!

    Pacto.configure do |c|
      c.contracts_path = 'contracts'
    end

    Pacto.generate!

    Net::HTTP.get('pactodex.herokuapp.com', '/pokemons')
end