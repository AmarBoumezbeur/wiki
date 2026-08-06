# Bash

1. Open a connection
```
PORT=9050
exec 3<> /dev/tcp/localhost/$PORT
```
2. Kill a connection 
```
exec 3>&-
```
3. Listen for a message
```
    while IFS= read -r line; do
    
    done
```

