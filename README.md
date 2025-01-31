in this project we are writing a scrit to check the system if it exceeded the thershold
here is my script code 
  GNU nano 7.2                                 monitor.sh                                          
#!/bin/bash

# Default values
THRESHOLD=80
LOGFILE="system_monitor.log"

# Parse command-line arguments
while getopts "t:f:" opt; do
  case ${opt} in
    t ) THRESHOLD=$OPTARG ;;
    f ) LOGFILE=$OPTARG ;;
    * ) echo "Usage: $0 [-t threshold] [-f logfile]"; exit 1 ;;
  esac
done

# Get current timestamp
TIMESTAMP=$(date "+%Y-%m-%d %H:%M:%S")

# Start report
echo -e "System Monitoring Report - $TIMESTAMP\n======================================\n" > $LOGFI>

# Check Disk Usage
echo "Disk Usage:" >> $LOGFILE
df -h | awk '{print $1, $2, $3, $4, $5, $6}' >> $LOGFILE
echo "" >> $LOGFILE

# Warn if any partition exceeds threshold
EXCEEDED=$(df -h | awk -v threshold=$THRESHOLD '$5+0 > threshold {print "Warning: "$1" is above "t>
if [[ ! -z "$EXCEEDED" ]]; then
  echo -e "\033[1;31m$EXCEEDED\033[0m"  # Red text for warning
  echo "$EXCEEDED" >> $LOGFILE
fi

# Check CPU Usage
CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print $2 + $4}')
echo -e "CPU Usage:\nCurrent CPU Usage: ${CPU_USAGE}%\n" >> $LOGFILE

# Check Memory Usage
MEMINFO=$(free -h | awk 'NR==2{print "Total Memory:", $2, "\nUsed Memory:", $3, "\nFree Memory:", >
echo -e "Memory Usage:\n$MEMINFO\n" >> $LOGFILE

# Check Top 5 Memory-Consuming Processes
echo -e "Top 5 Memory-Consuming Processes:\n" >> $LOGFILE
ps aux --sort=-%mem | awk 'NR<=6 {print $2, $1, $4, $11}' >> $LOGFILE

# Send Email Alert if Thresholds are Breached
if [[ ! -z "$EXCEEDED" ]] || (( $(echo "$CPU_USAGE > $THRESHOLD" | bc -l) )); then
  mail -s "System Monitoring Alert" omarsuwailem12@gmail.com < $LOGFILE
fi

# Display output
cat $LOGFILE

output screens
![image](https://github.com/user-attachments/assets/8dbe2075-71de-44ee-acf8-a9bdf1b35c0a)

![image](https://github.com/user-attachments/assets/c79cbd37-1f79-435c-9020-e06b40305f8f)
