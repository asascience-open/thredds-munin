THREDDS-Munin
=============
A Munin plugin for monitoring a THREDDS instance
--------------------------------------------

##### Requirements ####
-  Ruby >= 1.8.7 (Tested on ruby-1.8.7-p358 and ruby-1.9.3-p125)
-  THREDDS
-  Munin-node

##### Measurments ####

-  The number of webpage requests (a user browsing the catalog)
-  The number of errors
-  The number of requests for each type of data access:
  * DAP
  * HTTP
  * SOS
  * ISO
  * WMS
  * WCS
  * NCML
  * UDDC

[Samples](http://munin.maracoos.org/maracoos.org/tds.maracoos.org/thredds_requests.html "Samples")

#### Installation ####

Copy the thredds_ file into the **SYSTEM** munin plugins directory (usually /usr/share/munin/plugins)

    cp thredds_ /usr/share/munin/plugins
    chmod 755 /usr/share/munin/plugins/thredds_

Create symlinks in the **ACTIVE** munin plugins directory (usually /etc/munin/plugins)

    ln -s /usr/share/munin/plugins/thredds_ /etc/munin/plugins/thredds_errors
    ln -s /usr/share/munin/plugins/thredds_ /etc/munin/plugins/thredds_requests

Copy the thredds.config into the munin plugin configuration directory (usually /etc/munin/plugin-conf.d)

    cp thredds.config /etc/munin/plugin-conf.d/thredds

Edit the thredds config file to point to your threddsServlet.log file.  The contents should look like:

    [thredds_*]
      env.logfile PATH_TO_THREDDS_LOG_FOLDER/threddsServlet.log

Test the plugin.  You should see some values and not all "U" if your logfile has entries in it.

    munin-run thredds_requests
      dap.value 368
      http.value U
      sos.value U
      iso.value U
      wms.value U
      wcs.value U
      ncml.value U
      uddc.value U
      catalog.value 3

This plugin uses state files to track what the last timestamp in the logfile was that it processed.  Subsequent runs only looks at values **AFTER** that timestep.  After each run, the statefile is updated to be the last timestamp of the last line of the logfile.  **You must remove the state files inbetween tests!**  The files are usually located in /var/lib/muniun/plugin-state.


#### Monitoring  multiple THREDDS servers on the same Munin node ####

If you are running more than one THREDDS instance on the same munin-node, you will need to create symlinks and a configuration section for each running THREDDS server.  For example, to monitor a "production" and "staging" instance, create four symlinks:

    ln -s /usr/share/munin/plugins/thredds_ /etc/munin/plugins/thredds_production_errors
    ln -s /usr/share/munin/plugins/thredds_ /etc/munin/plugins/thredds_production_requests
    
    ln -s /usr/share/munin/plugins/thredds_ /etc/munin/plugins/thredds_staging_errors
    ln -s /usr/share/munin/plugins/thredds_ /etc/munin/plugins/thredds_staging_requests

The graphs in Munin will be thusly named "THREDDS Production" and "THREDDS Staging".

Next, edit the thredds.config file to look like this:

    [thredds_production_*]
      env.logfile PATH_TO_PRODUCTION_LOG_FOLDER/threddsServlet.log
      
    [thredds_staging_*]
      env.logfile PATH_TO_STAGING_LOG_FOLDER/threddsServlet.log

