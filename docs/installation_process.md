# Installation on Aura Linux machine
## September 2020


### instructions from "production.md":


- Prerequisites:
    - Install [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git), install or update [python3](https://docs.python-guide.org/starting/install3/linux/). You may also find a simple text editor (like emacs) is useful for fine tuning, and if you are using a headless server then [x11vnc](http://www.karlrunge.com/x11vnc/) is helpful.
        DONE
        
    - Add the following environment variables to your .profile: (feel free to use other directories):
        - MONGO_DATA=/home/user_name/data/mongodb/
        - PYSYS_CODE=/home/user_name/pysystemtrade
        - SCRIPT_PATH=/home/user_name/pysystemtrade/sysproduction/linux/scripts
        - ECHO_PATH=/home/user/echos
        - MONGO_BACKUP_PATH=/media/shared_network/drive/mongo_backup
        DONE in /home/arto/.profile
        
    - Create the following directories (again use other directories if you like, but you must modify the .profile above)
        - '/home/user_name/data/mongodb/'
        - '/home/user_name/echos/'
        
        DONE:
            using 2TB partition:  /mnt/sda1/tradingdata/mongodb
        
    - Install the pysystemtrade package, and install or update, any dependencies in directory $PYSYS_CODE (it's possible to put it elsewhere, but you will need to modify the environment variables listed above). If using git clone from your home directory this should create the directory '/home/user_name/pysystemtrade/'
        DONE
        (We now have a duplicate installation: one in /git location for development; /pysystemtrade is PROD )
        
    - [Set up interactive brokers](/docs/IB.md), download and install their python code, and get a gateway running.
        DONE
        
    - [Install mongodb](https://docs.mongodb.com/manual/administration/install-on-linux/)
        DONE. instructions for running it: [MongoDB manual](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)
        
    - create a file 'private_config.yaml' in the private directory of [pysystemtrade](#/private)
       DONE (empty file)
       
    - [check a mongodb server is running with the right data directory](/docs/futures.md#mongo-db) command line: `mongod --dbpath $MONGO_DATA`
        DONE
    - launch an IB gateway (this could be done automatically depending on your security setup)
       DONE
       
- FX data:
    - [Initialise the spot FX data in MongoDB from .csv files](/sysinit/futures/repocsv_spotfx_prices.py) (this will be out of date, but you will update it in a moment)
    - Check that you have got spot FX data present: command line:`. /pysystemtrade/sysproduction/linux/scripts/read_fx_prices`
        FILE DOES NOT EXIST
        
    - Update the FX price data in MongoDB using interactive brokers: command line:`. /home/your_user_name/workspace3/pysystemtrade/sysproduction/linux/scripts/update_fx_prices`
- Instrument configuration:
    - Set up futures instrument configuration using this script [instruments_csv_mongo.py](/sysinit/futures/instruments_csv_mongo.py).
- Roll calendars:
    - For *roll configuration* we need to initialise by running the code in this file [roll_parameters_csv_mongo.py](/sysinit/futures/roll_parameters_csv_mongo.py).
    - Create roll calendars for each instrument you are trading. Assuming you are happy to infer these from the supplied data [use this script](/sysinit/futures/rollcalendars_from_providedcsv_prices.py)
- Futures contract prices:
    - [If you have a source of individual futures prices, then backfill them into the Arctic database](/docs/futures.md#get_historical_data)
- Adjusted futures prices:
    - Create 'multiple prices' in Arctic. Assuming you have prices in Artic and roll calendars in csv use [this script](/sysinit/futures/multipleprices_from_arcticprices_and_csv_calendars_to_arctic.py). I recommend *not* writing the multiple prices to .csv, so that you can compare the legacy .csv data with the new prices
    - Create adjusted prices. Assuming you have multiple prices in Arctic use [this script](/sysinit/futures/adjustedprices_from_mongo_multiple_to_mongo.py)
- Live production backtest:
    - Create a yaml config file to run the live production 'backtest'. For speed I recommend you do not estimate parameters, but use fixed parameters, using the [yaml_config_with_estimated_parameters method of systemDiag](/systems/diagoutput.py) function to output these to a .yaml file.
- Scheduling:
    - Initialise the [supplied crontab](/sysproduction/linux/crontab). Note if you have put your code or echos somewhere else you will need to modify the directory references at the top of the crontab.
    - All scripts executable by the crontab need to be executable, so do the following: `cd $SCRIPT_PATH` ; `sudo chmod +x *.*`

