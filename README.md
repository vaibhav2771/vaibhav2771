sed -e '0,/<GlobalNamingResources>/,/<\/GlobalNamingResources>/d' server.txt > output.txt
sed '0,/<\/GlobalNamingResources>/{/<GlobalNamingResources>/,/<\/GlobalNamingResources>/d}' server.txt > output.txt



sed '0,/<GlobalNamingResources>/{/<GlobalNamingResources>/,/<\/GlobalNamingResources>/d;}' server.txt > output.txt



sed -n '1,/<GlobalNamingResources>/p; /<GlobalNamingResources>/,$ { /<GlobalNamingResources>/,/<\/GlobalNamingResources>/d; }' server.txt > output.txt

sed -i '0,/<GlobalNamingResources>/ { /<GlobalNamingResources>/,/<\/GlobalNamingResources>/d }' server.txt



sed -i '0,/<GlobalNamingResources>/ {/<GlobalNamingResources>/,/<\/GlobalNamingResources>/d}' server.txt

sed -i '0,/<GlobalNamingResources>/ {N;N;/<GlobalNamingResources>.*\n.*<Resource.*\n.*<\/GlobalNamingResources>/d}' server.txt



sed '0,/<GlobalNamingResources>/ { :a; N; /<\/GlobalNamingResources>/!ba; d }' server.txt
sed -i '0,/<GlobalNamingResources>/ { :a; N; /<\/GlobalNamingResources>/!ba; d; }' server.txt

sed '0,/<GlobalNamingResources>/ {/<GlobalNamingResources>/,/<\/GlobalNamingResources>/d}' server.txt

sed -i '0,/<GlobalNamingResources>/ {/<GlobalNamingResources>/,/<\/GlobalNamingResources>/d}' server.txt
sed -i '0,/<GlobalNamingResources>/ {/<GlobalNamingResources>/,/<\/GlobalNamingResources>/d}' server.txt


sed '0,/<GlobalNamingResources>/ {/^<GlobalNamingResources>/,/<\/GlobalNamingResources>/d}' server.txt
sed '0,/<GlobalNamingResources>/ { :a; N; /<\/GlobalNamingResources>/!ba; d }' server.txt

sed '0,/<GlobalNamingResources>/ {x; /<GlobalNamingResources>/ {x; :a; N; /<\/GlobalNamingResources>/!ba; d; }}' server.txt


sed -e '
  /<GlobalNamingResources>/,/<\/GlobalNamingResources>/ {
    /<GlobalNamingResources>/h;
    /<GlobalNamingResources>/n;
    :a
    /<\/GlobalNamingResources>/!{
      N;
      ba
    };
    /<\/GlobalNamingResources>/g;
    d
  }
' server.txt > output.txt


sed -i '0,/<GlobalNamingResources>/ {/<GlobalNamingResources>/,/<\/GlobalNamingResources>/ {d;};}' server.txt


