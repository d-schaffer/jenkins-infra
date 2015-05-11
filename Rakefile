require 'puppet-lint'
require 'puppetlabs_spec_helper/rake_tasks'

desc "Validate the Puppet syntax of all manifests"
task :validate do
  Dir['./{dist,manifests}/**/*.pp'].each do |filename|
    sh "puppet parser validate '#{filename}'"
  end
end

desc 'Run puppet-lint on Puppet manifests defined in this repo'
task :lint do
  # Including puppet-lint rake tasks after the puppetlabs_spec_helper tasks to
  # make sure that the pl_s_h lint task doesn't overwrite our configuration below
  require 'puppet-lint/tasks/puppet-lint'

  PuppetLint.configuration.send('disable_80chars')
  PuppetLint.configuration.send('disable_class_parameter_defaults')
  PuppetLint.configuration.send('disable_names_containing_dash')
  PuppetLint.configuration.ignore_paths = ['modules/**/*.pp', 'spec/fixtures/**/*.pp', 'vendor/**/*.pp']
  PuppetLint.configuration.fail_on_warnings = true
end

desc 'Resolve all the dependencies'
task :resolve do
  # for reasons beyond me, we list dependencies in Puppetfile and .fixtures.yml
  # we need to keep them in sync, and when we change them we need to run two commands
  # to reflect those changes

  # this fills ./modules
  `rm -rf ./modules/*`
  `r10k puppetfile install`

  # this fills ./spec/fixtures/modules
  Rake::Task['spec_clean'].invoke
  Rake::Task['spec_prep'].invoke
end

desc 'Check syntax of DNS zone file'
task "test-zonefile" do
  sh "docker run --rm -v $PWD:/data kohsuke/named-checkzone jenkins-ci.org dist/profile/files/bind/jenkins-ci.org.zone"
end