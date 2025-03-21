sed -e '0,/<GlobalNamingResources>/,/<\/GlobalNamingResources>/d' server.txt > output.txt
sed '0,/<\/GlobalNamingResources>/{/<GlobalNamingResources>/,/<\/GlobalNamingResources>/d}' server.txt > output.txt
