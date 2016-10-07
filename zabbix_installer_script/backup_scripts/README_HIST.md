
Environment:

Zabbix v2.2

MySql 5.1 – InnoDB with innodb_file_per_table=ON

### Step 1 – Stop the Zabbix server

	sudo service zabbix-server stop

### Step 2 – Open your favourite MySQL client and create a new table

	CREATE TABLE history_uint_new_20161007 LIKE history_uint;

### Step 3 – Insert the latest records from the history_uint table to the history_uint_new table

Getting `epoch` time in `bash` is simple. 

Current Date.

	date --date "20160707" +%s

Date 3 Months Ago.

	date --date "20161007" +%s

	[ahmed@localhost ~]$ date --date "20160707" +%s
	1467829800
	[ahmed@localhost ~]$ date --date "20161007" +%s
	1475778600

Now insert data for 3 months.  

INSERT INTO history_uint_new SELECT * FROM history_uint WHERE clock > '1413763200';

### Step 4 – Rename the history_uint and history_uint_new tables

	ALTER TABLE history_uint RENAME history_uint_old;
	ALTER TABLE history_uint_new_20161007 RENAME history_uint;

### Step 5 – Start the Zabbix server

	sudo service zabbix-server start

Start the Zabbix server and check if everything is ok and if you’re happy…

### Step 6 – Drop the old table and save some disk space

    DROP TABLE history_uint_old;

Additionally you can update the items table and set the item history table record to a fewer days, so Zabbix will do the automatic clean up before the table size gets too big.

    UPDATE items SET history = '15' WHERE history > '30';
    
    
    
    
    sudo service zabbix-server stop; 
    echo "show databases; use zabbix; show tables;CREATE TABLE history_uint_new_20161007 LIKE history_uint; INSERT INTO history_uint_new_20161007 SELECT * FROM history_uint WHERE clock > '1413763200'; ALTER TABLE history_uint RENAME history_uint_old;ALTER TABLE history_uint_new_20161007 RENAME history_uint; " | mysql -uroot -p;
    
    echo "show databases; use zabbix; show tables; drop table history_uint_new_20161007" | mysql -uroot -p



### Other scribble. 

    date -d "-$(date +%d) day" +%Y-%m-%d
    
    date -d "now -3months" +%Y-%m-%d
    
    THREE_MONTH_BACK_DATE=`date -d "now -3months" +%Y-%m-%d`
    CURRENT_DATE=`date -d "now" +%Y-%m-%d`
    
    EPOCH_3MONTHS_BACK=`date -d "$THREE_MONTH_BACK_DATE" +%s`
    EPOCH_NOW=`date -d "$CURRENT_DATE" +%s`
    
    
    sudo service zabbix-server stop; 
    
    echo " 
    show tables;
    CREATE TABLE history_uint_new_20161007 LIKE history_uint; 
    INSERT INTO history_uint_new_20161007 SELECT * FROM history_uint WHERE clock > '${EPOCH_3MONTHS_BACK}'; 
    ALTER TABLE history_uint RENAME history_uint_old;
    ALTER TABLE history_uint_new_20161007 RENAME history_uint; " | mysql -uroot -p;
    
    sudo service zabbix-server start;

