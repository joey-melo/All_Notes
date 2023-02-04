```php
function register(){
    $data = array(
        'username' => 'hacker3',
        'email' => 'hacker3@user.com',
        'password' => 'hacker',
        'password-confirm' => 'hacker'
    );

    $options = array(
        'http' => array(
            'header'  => "Content-type: application/x-www-form-urlencoded\r\n",
            'method'  => 'POST',
            'content' => http_build_query($data)
        ),
        'ssl' => array(
            'verify_peer' => false,
            'verify_peer_name' => false,
        )
    );
    $context  = stream_context_create($options);
    $result = file_get_contents('https://broscience.htb/register.php', false, $context);
    return $result;
};
```