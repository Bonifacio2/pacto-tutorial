require 'pacto'

task :generate do
    WebMock.allow_net_connect!

    Pacto.configure do |c|
      c.contracts_path = 'contracts'
    end

    Pacto.generate!

    Net::HTTP.get('pactodex.herokuapp.com', '/pokemons')
end

task :validate do
  WebMock.allow_net_connect!
  contracts = Pacto.load_contracts('contracts', 'pactodex.herokuapp.com')
  test_results = contracts.validate_all
  if test_results.all?(&:empty?)
    puts 'success'
  else
    puts test_results
    fail
  end
end
