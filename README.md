
for d in /eClinicalWorks/prodapps/*/; do [[ ! -L /etc/init.d/"${d##*/}" ]] && echo "${d##*/}"; done
