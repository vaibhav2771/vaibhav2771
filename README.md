sed -e '0,/<GlobalNamingResources>/,/<\/GlobalNamingResources>/d' server.txt > output.txt
sed '0,/<\/GlobalNamingResources>/{/<GlobalNamingResources>/,/<\/GlobalNamingResources>/d}' server.txt > output.txt



sed '0,/<GlobalNamingResources>/{/<GlobalNamingResources>/,/<\/GlobalNamingResources>/d;}' server.txt > output.txt



sed -n '1,/<GlobalNamingResources>/p; /<GlobalNamingResources>/,$ { /<GlobalNamingResources>/,/<\/GlobalNamingResources>/d; }' server.txt > output.txt

sed -i '0,/<GlobalNamingResources>/ { /<GlobalNamingResources>/,/<\/GlobalNamingResources>/d }' server.txt

