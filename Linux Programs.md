# Scripts as Linux Programs

## Setting Up Executables

In the file that will run as executable add the shebang to the top of the file

```
#!/path/to/script/interpreter
```
- For Bash scripts: `#!/bin/bash`
- For Perl scripts: `#!/bin/perl`
- For Python 2 scripts: `#!/bin/python`
- For Python 3 scripts: `#!/bin/python3`

Add executable permissions: `chmod +x my_script`.

It is good to to remove file extension from file name, but not required.

## Source Code

Following the Linux Filesystem Hierarchy Standard:

- Source code must be placed in `/usr/local/share/program-name/`
- Logs and error data in `/var/log/program-name/`
- Pid files in `/var/run/`
- System wide configuration for the program in `/etc/program-name/`
- Temporary files in `/tmp/`

## System Wide Use

Creates links for source code executables and place them in `/usr/local/bin/`

For example:

```
$ ln -s /usr/local/share/program-name/my_exec /usr/local/bin/any_name
```

It will add `any_name` to system path and it will run `/usr/local/share/program-name/my_exec`


## Run Script as Background Service

Instead of running the script straight away, use a controller bash script that will handle the service execution and call the main script.

The bash script handler will:

- Setup the enviroment:
	* Install dependencies.
	* Create required folders and files.
	* Set permissions.
	* Link itself and other files (if necessary) to files at `/usr/local/bin/`.
	* Do everything that we talked previously in this document.
- Purge: undo everything we talked in the previous item.
- Start service and dettach from session with nohup (let it running in background).
- Repository update: pull updates from git.
- Check status.

Below, there is a script that I wrote for controlling python script based background services that do everything I said above:

```
#!/bin/bash

################################################################################
#                             CONSTANT DECLARATIONS                            #
################################################################################
PID_FILE=/var/run/egs-petro-report.pid
LOG_DIR=/var/log/egs-petro-report

################################################################################
#                                HELPER FUNCTIONS                              #
################################################################################
# Check if program is running and store result in $RUNNING
isRunning() {
    touch $PID_FILE
    PID=$(cat $PID_FILE)
    PID_SIZE=$(echo $PID | wc -w)
    RUNNING=false
    if [[ $PID_SIZE == "1" ]]; then
        PROCS_SIZE=$(ps -f --pid $PID --no-headers | wc -l)
        if [[ $PROCS_SIZE == "1" ]]; then
            RUNNING=true
        fi
    fi
}

# Get executable path and store result in $DIR
execPath() {
    SOURCE="${BASH_SOURCE[0]}"
    while [ -h "$SOURCE" ]; do
        DIR="$( cd -P "$( dirname "$SOURCE" )" && pwd )"
        SOURCE="$(readlink "$SOURCE")"
        [[ $SOURCE != /* ]] && SOURCE="$DIR/$SOURCE"
    done
    DIR="$( cd -P "$( dirname "$SOURCE" )" && pwd )"
}

################################################################################
#                                     MAIN                                     #
################################################################################

if [[ $1 = "setup" ]]; then
    # Install dependencies
    sudo apt-get install python-pip python-dev libmysqlclient-dev
    pip2 install mysqlclient
    pip2 install boto3
    # Setup directorie and files
    execPath
    sudo mkdir -p $LOG_DIR/backup
    sudo touch $PID_FILE
    sudo touch $LOG_DIR/dux-module.log
    sudo touch $LOG_DIR/dux-module.error
    sudo chown $USER:$USER $PID_FILE
    sudo chown -R $USER:$USER $LOG_DIR
    sudo chown -R $USER:$USER $DIR
    sudo cp -n $DIR/egs-petro-report.conf /etc/egs-petro-report.conf
    sudo ln -s $DIR/petro-report /usr/local/bin/egs_petro_report
    sudo ln -s $DIR/petro-report-ftime /usr/local/bin/egs_petro_report_ftime
    echo ""
    echo "ALL SET."
elif [[ $1 = "purge" ]]; then
    sudo rm -r $LOG_DIR
    sudo rm $PID_FILE
    sudo rm /usr/local/bin/egs_petro_report
    sudo rm /usr/local/bin/egs_petro_report_ftime
    sudo rm /etc/egs-petro-report.conf
    echo "PURGED."
elif [[ $1 = "start" ]]; then
    isRunning
    execPath
    if [[ $RUNNING == "false" ]]; then
        truncate -s 0 $PID_FILE
        echo "Starting daemon..."
        nohup python2.7 $DIR/petro-report.py >/dev/null 2>&1 &
        echo "OK"
    else
        echo "Daemon is already running. PID: $PID"
    fi
elif [[ $1 = "cstart" ]]; then
    execPath
    python2.7 $DIR/petro-report.py -c
elif [[ $1 = "stop" ]]; then
    isRunning
    if [[ $RUNNING == "true" ]]; then
        kill $PID
        echo "Killed process $PID"
    else
        echo "Daemon is not running"
    fi
    truncate -s 0 $PID_FILE
elif [[ $1 = "clear" ]]; then
    CURR_DATE=`date +%Y%m%d%H%M%S`
    cp $LOG_DIR/dux-module.log $LOG_DIR/backup/$CURR_DATE'_dux-module.log.bak'
    cp $LOG_DIR/dux-module.error $LOG_DIR/backup/$CURR_DATE'_dux-module.error.bak'
    truncate -s 0 $LOG_DIR/dux-module.log
    truncate -s 0 $LOG_DIR/dux-module.error
elif [[ $1 = "update" ]]; then
    execPath
    cd $DIR
    git pull
elif [[ $1 = "version" ]]; then
    execPath
    cd $DIR
    git show --oneline -s
elif [[ $1 = "status" ]]; then
    isRunning
    if [[ $RUNNING == "true" ]]; then
        echo "RUNNING - PID $PID"
        echo ""
        ps -o etime,%cpu,%mem,args --pid $PID
    else
        echo "NOT RUNNING"
    fi
elif [[ $1 = "history" ]]; then
    cat $LOG_DIR/dux-module.log| grep "STARTED SERVICE AT"
else
    echo -e "\r\nEGS PETRO REPORT SERVICE"
    echo -e "Sends automatic reports through email and ftp.\r\n\r\nParameters:\r\n"
    echo -e "\tstart: starts the service."
    echo -e "\tcstart: start as console application."
    echo -e "\tpurge: remove all service related files."
    echo -e "\tstop: stops the service."
    echo -e "\tstatus: show status."
    echo -e "\tsetup: install dependencies, create folders, files and set permissions."
    echo -e "\tclear: backup and clear log files."
    echo -e "\tupdate: update by pulling from git repository."
    echo -e "\tversion: print current version from git repository.\r\n"
fi
``` 

The Python script referenced in this bash script is pretty much a infinite loop that executes a main function every N seconds. The python script will also redirect output to log files created in the above scritp and its pid too.