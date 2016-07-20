#puppetdbquery
A script that query your PuppetDB and/or console database for status, runs, events, facts, metrics, ...
It aims at bringing the console/dashboard informations to the command line

##This script is compatible with :
* 3.x / 2015.x / 2016.x Puppet Enterprise versions
* 4.x Community versions

##Prerequisite

This script needs absolutly no prerequisite but a bash shell, it can query local database (default) or distant one with split installation for example.
But in order to query the console and/or puppetdb database and for security reason you will need to create a readonly user for those databases, you can use the addreadonly2database script in this repo to do that.

##Configuration

You probably need to configure some parameters of this script to reflect your Puppet configuration:

        # Timezone
        TZ="CET"

        # LOCALDATE format for postgresql
        LOCALDATE="set datestyle='SQL,DMY'"

        # By default order by time
        ORDER="time"

        # By default print the 20 lasts reports like PE dashboard
        NBLINE="20"

        # Login/Password to acces the database
        USERNAME="readonly"
        export PGPASSWORD=$USERNAME

        # Set default time delay (2h)
        TIME="120"

        # Set unresponsive time delay
        UNRESPONSIVETIME="108000"

        # Postgres Puppetdb/console database hostname
        HOST=localhost

##Here are the possibles options for the script:

        puppetdbquery [-t run] [-h] [-c] [-f] [-r] [-a] [-C] [-m] [-F] [-R] [-A <number>] [-S <failed|noop|change|unchanged|unreported|unresponsive>] [-E <environment>] [<hostname> <hostname> ...]
       -a => all, by default only the 20 last nodes runs are print
       -A => number of older report to print, by default only the last report is print
       -S => status <failed|noop|change|unchanged|unreported|unresponsive>
       -E => environment, select hostname by environment
       -r => report, print the last report of a run node
       -R => resources, print the resources used in a run node
       -F => facts, print the facts used in a run node
       -m => metrics, print the metrics of a run node
       -L => catalog, print the catalog of a run node
       -c => count, give a global status count in the header
       -f => force color even if we don't use a terminal
       -h => help
       <hostname> parameter and -a parameters are mutually exclusive

       puppetdbquery [-t event] [-h] [-f] [-v] [-T <number_of_minutes] [-d <YYYY-MM-DD hh:mm>] [-D <YYYY-MM-DD hh:mm>] [-o <hostname|time|status|class|resource_type|resource_title>] [-s <desc|asc>] [-H hostname] [-C class] [-S <failed|noop|changed|unchanged>] [-E <environment>]
       -T => specify a time period in minutes, 120 minutes is the default
       -d => specify a time period from <YYYY-MM-DD hh:mm> to now
       -D => specify a time period from -d parameter to <YYYY-MM-DD hh:mm>
       -o => specify the column to sort <hostname|time|status|class|resource_type|resource_title>
       -s => specify the sort order <desc|asc>
       -H => restrict ouput to a hostname like h|host|hostname
       -C => restrict ouput to a classname like Ora|Oradb|Oradb::node, Class parameter is case sensitive
       -S => restrict ouput to a status <failed|noop|changed|unchanged>
       -E => restrict ouput to an environment
       -v => verbose, print more event information
       -f => force color even if we don't use a terminal
       -h => help

       puppetdbquery [-t status] [-h] [-f] [-v] [-T <number_of_minutes] [-d <YYYY-MM-DD hh:mm>] [-D <YYYY-MM-DD hh:mm>] [-S <failed|noop|changed|unchanged>] [-E <environment>] [-s <desc|asc>]
       -T => specify a time period in minutes, 120 minutes is the default
       -d => specify a time period from <YYYY-MM-DD hh:mm> to now
       -D => specify a time period from -d parameter to <YYYY-MM-DD hh:mm>
       -S => specify a status <failed|noop|changed|unchanged>, failed is the default status
       -E => restrict ouput to an environment
       -s => specify the sort order <desc|asc>
       -v => verbose, print more status information
       -f => force color even if we don't use a terminal
       -h => help

##Use cases

Note : Readme.md can't handle color text, but in those examples there should be colors, for instance : failed=red, noop=yellow, unchanged=green, changed=blue, ...

###To know the last run status
        $ puppetdbquery -t run -c
        Global Status: Unresponsive:0  Failed:7       Noop:9       All:238
                       Unreported:0    Unchanged:90   Changed:132  Hidden:0
        Node          Latest report     Status    Total Failed Noop Changed Unchanged
        xxxxxxxx      20/07/2016 12:47  failed    0     0      0    0       0
        xxxxxxxx      20/07/2016 12:40  noop      545   0      4    0       541
        xxxxxxxx      20/07/2016 12:38  changed   613   0      0    23      590
        xxxxxxxx      20/07/2016 12:35  noop      155   0      20   0       135
        xxxxxxxx      20/07/2016 12:07  changed   466   0      0    73      393
        xxxxxxxx      20/07/2016 12:00  failed    0     0      0    0       0
        xxxxxxxx      20/07/2016 11:37  noop      443   0      17   0       426
        xxxxxxxx      20/07/2016 10:44  changed   264   0      0    4       260
        xxxxxxxx      20/07/2016 05:21  failed    541   1      2    0       538
        xxxxxxxx      20/07/2016 05:20  noop      292   0      4    0       288
        xxxxxxxx      20/07/2016 05:20  noop      292   0      4    0       288
        xxxxxxxx      20/07/2016 05:20  noop      292   0      4    0       288
        xxxxxxxx      20/07/2016 05:20  failed    0     0      0    0       0
        xxxxxxxx      20/07/2016 05:19  noop      948   0      1    0       947
        xxxxxxxx      20/07/2016 05:19  noop      292   0      6    0       286
        xxxxxxxx      20/07/2016 05:19  noop      292   0      4    0       288
        xxxxxxxx      20/07/2016 05:19  failed    0     0      0    0       0
        xxxxxxxx      20/07/2016 05:19  failed    0     0      0    0       0
        xxxxxxxx      20/07/2016 05:19  noop      264   0      4    0       260
        xxxxxxxx      20/07/2016 05:19  noop      292   0      7    0       285
###To see the last changed report run of the xxxxxxx agent
        $ puppetdbquery -t run -S changed -r xxxxxxxx
        Node          Latest report     Status    Total Failed Noop Changed Unchanged
        xxxxxxxx      20/07/2016 14:02  changed   948   0      0    2       946
        -----------------------------------------------------------------------------
        Node          Time - Level - Message - Source - File - Line
        xxxxxxxx      14:03 20/07/2016 - notice - content changed '{md5}5da38575e7aaaabaa4a447a3d036fb63' to         '{md5}0adf5dc53647ef36b48e9fde11e540f7' - /Stage[main]/System::Yumrepo/File[dvd.repo]/content - /home/puppet/environments/development/modules/system/manifests/yumrepo.pp - 70
        xxxxxxxx      14:03 20/07/2016 - notice - content changed '{md5}7165d7dcd3f133a5f30f70cae3d0fa71' to '{md5}24d42aea36eb600fc9fcc3ef39ab6138' - /Stage[main]/System::Motd/File[/etc/motd]/content - /home/puppet/environments/development/modules/system/manifests/motd.pp - 24
        xxxxxxxx      14:05 20/07/2016 - notice - Finished catalog run in 29.02 seconds - Puppet
        -----------------------------------------------------------------------------
###To see the facts of the latest xxxxxxx agent run
        $ puppetdbquery -t run -F xxxxxxx
        Node          Latest report     Status    Total Failed Noop Changed Unchanged
        xxxxxxxx      20/07/2016 14:37  changed   948   0      0    11      937
        -----------------------------------------------------------------------------
        Facts
        architecture => x86_64
        augeasversion => 1.3.0
        bios_release_date => 09/17/2015
        bios_vendor => Phoenix Technologies LTD
        bios_version => 6.00
        blockdevices => sda,sdb,sdc,sr0
        blockdevice_sda_model => Virtual disk
        blockdevice_sda_size =>
        blockdevice_sda_vendor => VMware
        blockdevice_sdb_model => Virtual disk
        blockdevice_sdb_size =>
        blockdevice_sdb_vendor => VMware
        blockdevice_sdc_model => Virtual disk
        blockdevice_sdc_size =>
        blockdevice_sdc_vendor => VMware
        blockdevice_sr0_model => VMware IDE CDR10
        blockdevice_sr0_size =>
        blockdevice_sr0_vendor => NECVMWar
        boardmanufacturer => Intel Corporation
        boardproductname => 440BX Desktop Reference Platform
        boardserialnumber => None
        certificat_csr => {}
        certificat_revocation => []
        clientversion => 3.8.5 (Puppet Enterprise 3.8.4)
        ...
        virtual => vmware
        -----------------------------------------------------------------------------

### To see the metrics of the latest xxxxxxx agent run
        $ puppetdbquery -t run -m xxxxxxx
        Node          Latest report     Status    Total Failed Noop Changed Unchanged
        xxxxxxxx      20/07/2016 14:37  changed   948   0      0    11      937
        -----------------------------------------------------------------------------
        Metrics                                 - Time in seconds
        filebucket                             -    0.00 s
        group                                  -    0.00 s
        om_config                              -    0.00 s
        pe_anchor                              -    0.00 s
        schedule                               -    0.00 s
        yumrepo                                -    0.00 s
        cron                                   -    0.01 s
        pe_file_line                           -    0.01 s
        pe_ini_subsetting                      -    0.01 s
        user                                   -    0.01 s
        pe_ini_setting                         -    0.02 s
        sysctl                                 -    0.23 s
        pe_java_ks                             -    2.10 s
        exec                                   -    4.02 s
        package                                -    4.88 s
        augeas                                 -    8.94 s
        file                                   -   25.04 s
        service                                -   45.46 s
        config_retrieval                       -   50.13 s
        total                                  -  140.86 s
        run real time overall                  -  182.00 s (5 resources/s)
        -----------------------------------------------------------------------------

###To see the last events
        $ puppetdbquery -t event
        Hostname Date Time Status Class Resource_type Resource_title
        xxxxxxxx 20/07/2016 15:35:52 noop Puppet_enterprise::Mcollective::Service Service pe-mcollective
        xxxxxxxx 20/07/2016 15:35:39 noop Puppet_enterprise::Mcollective::Server::Facter Scheduled_task pe-mcollective-metadata
        xxxxxxxx 20/07/2016 15:35:39 noop Puppet_enterprise::Mcollective::Server File C:\ProgramData/PuppetLabs/mcollective/etc/server.cfg
        xxxxxxxx 20/07/2016 15:35:39 noop Puppet_enterprise::Mcollective::Server::Certs File C:\ProgramData/PuppetLabs/mcollective/etc/ssl/xxxxxxxx.private_key.pem
        xxxxxxxx 20/07/2016 15:35:39 noop Puppet_enterprise::Mcollective::Server::Certs File C:\ProgramData/PuppetLabs/mcollective/etc/ssl/xxxxxxxx.cert.pem
        xxxxxxxx 20/07/2016 15:35:39 noop Puppet_enterprise::Mcollective::Server::Plugins File C:/ProgramData/PuppetLabs/mcollective/etc/plugins/mcollective/util/puppet_agent_mgr.rb
        xxxxxxxx 20/07/2016 15:35:39 noop Puppet_enterprise::Mcollective::Server::Plugins File C:/ProgramData/PuppetLabs/mcollective/etc/plugins/mcollective/util/package/packagehelpers.rb
        xxxxxxxx 20/07/2016 15:35:39 noop Puppet_enterprise::Mcollective::Server::Plugins File C:/ProgramData/PuppetLabs/mcollective/etc/plugins/mcollective/util/package/puppetpackage.rb
        xxxxxxxx 20/07/2016 15:35:38 noop Puppet_enterprise::Mcollective::Server::Plugins File C:/ProgramData/PuppetLabs/mcollective/etc/plugins/mcollective/security/sshkey.rb
        xxxxxxxx 20/07/2016 15:35:38 noop Puppet_enterprise::Mcollective::Server::Plugins File C:/ProgramData/PuppetLabs/mcollective/etc/plugins/mcollective/application/service.rb
        xxxxxxxx 20/07/2016 15:35:38 noop Puppet_enterprise::Mcollective::Server::Plugins File C:/ProgramData/PuppetLabs/mcollective/etc/plugins/mcollective/application/package.rb
        xxxxxxxx 20/07/2016 15:35:38 noop Puppet_enterprise::Mcollective::Server::Plugins File C:/ProgramData/PuppetLabs/mcollective/etc/plugins/mcollective/agent/service.ddl
        xxxxxxxx 20/07/2016 15:35:38 noop Puppet_enterprise::Mcollective::Server::Plugins File C:/ProgramData/PuppetLabs/mcollective/etc/plugins/mcollective/agent/puppet.ddl
        xxxxxxxx 20/07/2016 15:35:38 noop Puppet_enterprise::Mcollective::Server::Plugins File C:/ProgramData/PuppetLabs/mcollective/etc/plugins/mcollective/agent/puppet.rb
        xxxxxxxx 20/07/2016 15:35:38 noop Puppet_enterprise::Mcollective::Server::Plugins File C:/ProgramData/PuppetLabs/mcollective/etc/plugins/mcollective/agent/package.rb
        xxxxxxxx 20/07/2016 15:35:38 noop Puppet_enterprise::Mcollective::Server::Plugins File C:/ProgramData/PuppetLabs/mcollective/etc/plugins/mcollective/agent/package.ddl

### To know the number of agents changed status by module for the last 24 hours
        puppetdbquery-gitlab -t status -T 1440 -S changed -s desc
        Apache            : 87
        Mongo             : 65
        Spark             : 62
        Virtual_resources : 39
        Puppet_enterprise : 22
        Logstash          : 10
        Elasticsearch     : 8
        Batch             : 5
        Autofs            : 1
