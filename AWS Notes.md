# AWK GUIDE

Pedro Dousseau
November 8th, 2017

### REMOVE LINE FEED AND SWAP ROWS

````
awk -F 'FS' 'BEGIN{FS=","} {
gsub(/[\n\r]/,"");
print $1 FS $12 FS $4 FS $5 FS $6 FS $7 FS $8 FS $9 FS $10 FS $11 FS $2 FS $3
};' file.dat
````