##### Install PHPMailer package using composer

```
composer require phpmailer/phpmailer
```

##### Setup .env file for PHPMailer from mailtrap

```
CMAIL_HOST='sandbox.smtp.mailtrap.io'

CMAIL_USERNAME='e9bae8104cd4a0'

CMAIL_PASSWORD='******eb368abb2'

CMAIL_ENCRIPTION='TLS'

CMAIL_PORT=2525

CMAIL_FROM_ADDRESS='admin@gmail.com'

CMAIL_FROM_NAME="${APP_NAME}"
```

Take in this mail configuration  details from [mailtrap](https://mailtrap.io/)

##### Configure config/services.php in laravel

```php
'mail' => [
        'host' => env('CMAIL_HOST'),
        'username' => env('CMAIL_USERNAME'),
        'password' => env('CMAIL_PASSWORD'),
        'encryption' => env('CMAIL_ENCRYPTION'),
        'port' => env('CMAIL_PORT'),
        'from_address' => env('CMAIL_FROM_ADDRESS'),
        'from_name' => env('CMAIL_FROM_NAME')
    ],
```

Configure mail information from .env file into config/services.php file in laravel.

##### Make laravel helper class for send mail

```php
// app/Helpers/CMail.php
<?php

namespace App\Helpers;

use PHPMailer\PHPMailer\PHPMailer;
use PHPMailer\PHPMailer\Exception;

class CMail
{
    public static function send($config)
    {
        //Create an instance; passing `true` enables exceptions
        $mail = new PHPMailer(true);

        try {
            //Server settings
            $mail->SMTPDebug = 0;                      //Enable verbose debug output
            $mail->isSMTP();                                            //Send using SMTP
            $mail->Host       = config('services.mail.host');                     //Set the SMTP server to send through
            $mail->SMTPAuth   = true;                                   //Enable SMTP authentication
            $mail->Username   = config('services.mail.username');                     //SMTP username
            $mail->Password   = config('services.mail.password');                               //SMTP password
            $mail->SMTPSecure = config('services.mail.encryption');            //Enable implicit TLS encryption
            $mail->Port       = config('services.mail.port');                                    //TCP port to connect to; use 587 if you have set `SMTPSecure = PHPMailer::ENCRYPTION_STARTTLS`
            //dd(config('services.mail.from_address'));
            //Recipients
            $mail->setFrom(
                isset($config['from_address']) ? $config['from_address'] : config('services.mail.from_address'),
                isset($config['from_name']) ? $config['from_name'] : config('services.mail.from_name')
            );
            $mail->addAddress($config['recipient_address'], isset($config['recipient_name']) ? $config['recipient_name'] : null);     //Add a recipient

            //Content
            $mail->isHTML(true);                                  //Set email format to HTML
            $mail->Subject = $config['subject'];
            $mail->Body    = $config['body'];

            if (!$mail->send()) {
                return false;
            } else {
                return true;
            }
        } catch (Exception $e) {
            dd($e);
        }
    }
}


```

All mail detail came from config/services.php file and mail data form user using ``send($config)`` method.

##### Now, use CMail class when send the mail

```php
$mail_body = view('eample.email-templates.forgot-template', $data)->render();
$mail_config = array(
    'from_address' => 'hello@gmail.com',
    'recipient_address' => $user->email,
    'recipient_name' => $user->name,
    'subject' => 'Reset Password',
    'body' => $mail_body
    );

CMail::send($mail_config) // send mail

```

We must be use CMail class. Like:  ``use App\Helpers\CMail;