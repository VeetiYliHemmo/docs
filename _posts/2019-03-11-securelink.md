secure link

 - expiration, access control, ...

Sometimes site owners need to share file(s) to a restricted group. This could be anything - PDFs to subscribers, xx for zz, you name it.

Quite common way to implement this has been (as we se it) to write PHP script, that does some `$authentication_and_authorization` magic, and then streams the actual content to the user. This has caveat that PHP needs to process deliverables, which consumes excessive resources (CPU, memory, disk I/O). Alternative would be to do access/authorization verification in PHP, and then deliver user a link that restricts access to specific user, and let nginx do the delivery - this speeds up the process, and reduces resource usage. Also, instead of writing custom file reading etc. functionality you need just to setup small nginx configuration and write one simple PHP function for generating "secure download links". Everyone wins.

This module has been hardcoded to use MD5 as hash algorithm, which has pros and cons. It's really fast to calculate these hashes, on the other hand it's also easier to break these hashes, so you need to take special care when selecting the secret - use long and unique value. You can also embed more information in the hash.


## Example
We have 
```nginx
location ~ \.pdf$ {
    # määritellään GET-parametrit, joiden arvoja käytetään secure linkkien generoinnissa
    secure_link $arg_md5,$arg_expires;
    
    # määritellään MD5-hashin muoto
    secure_link_md5 "$secure_link_expires$uri$remote_addr salaisuus";

    if ($secure_link = "") { 
        return 403;
    }
    if ($secure_link = "0") {
        return 410;
    }
}
```


?md5=X&expires=Y

<?php
function securelink($path) {
    $secret = getenv('MYSECRET');
    $expires = time() + 86400; // nyt + 86400s = +24h  
}
?>


=> 

echo -n "$expires/PATH$IP salaisuus" | openssl md5 -binary |openssl base64 |tr +/ -_ |tr -d =


misc
 - store secrets outside docroot, use nginx alias

