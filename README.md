# nwnee_php
PHP libraries for interfacing with Neverwinter Nights Extended Edition (NWNEE) servers

The functions in this "library" were created and tested under PHP 5.6.34.

# EXAMPLE

```php
#!/usr/bin/php
<?php
require 'nwnee_php/proto.php';

function nwnee_server_status($hostname, $port) {
    $fp = fsockopen(
        "udp://{$hostname}",
        $port,
        $errno,
        $errstr,
        2
    );

    if (!$fp) {
        return array(
            'connected' => false,
            'error'     => "{$errno}: {$errstr}",
        );
    }

    $bner_array = send_bnes_recv_bner($fp);
    if (array_key_exists('error', $bner_array)) {
      $close = fclose($fp);
      return array(
        'connected' => false,
        'error'     => $bner_array['error']
      );
    }
    print("======== send_bnes_recv_bner() ========\n");
    foreach ($bner_array as $key => $value) {
      printf("%-21s => %s\n", $key, $value);
    }
    print("\n\n");

    $bndr_array = send_bnds_recv_bndr($fp);
    if (array_key_exists('error', $bner_array)) {
      $close = fclose($fp);
      return array(
        'connected' => false,
        'error'     => $bner_array['error']
      );
    }
    print("======== send_bnds_recv_bndr() ========\n");
    foreach ($bndr_array as $key => $value) {
      printf("%-21s => %s\n", $key, $value);
    }
    print("\n\n");

    $bnxr_array = send_bnxi_recv_bnxr($fp);
    if (array_key_exists('error', $bner_array)) {
      $close = fclose($fp);
      return array(
        'connected' => false,
        'error'     => $bner_array['error']
      );
    }
    print("======== send_bnxi_recv_bnxr() ========\n");
    foreach ($bnxr_array as $key => $value) {
      printf("%-21s => %s\n", $key, $value);
    }

    $close = fclose($fp);
    if (!$close) {
        return array(
            'connected' => false,
            'error'     => "Failed to close socket"
        );
    }
}
nwnee_server_status('nwnserver.com', 5121);
?>
```

Output:
```
======== send_bnes_recv_bner() ========
header                => BNER
protocol              => 85
port                  => 5121
enumType              => 0
sessionNameSize       => 23
sessionName           => "My dick-kicking NWN server"


======== send_bnds_recv_bndr() ========
header                => BNDR
port                  => 5121
serverDescriptionSize => 0
serverDescription     => 
moduleDescriptionSize => 238
moduleDescription     => http://nwnserver.com
nwnserver@gmail.com
IRC    : irc.freenode.net #nwnserver
discord: https://discord.gg/6969696 

This is a really great server!
serverVersionSize     => 4
serverVersion         => 8
gameType              => 13873


======== send_bnxi_recv_bnxr() ========
header                => BNXR
port                  => 5121
bnxiVersionNumber     => 253
hasPassword           => 0
minLevel              => 1
maxLevel              => 20
currentPlayers        => 69
maxPlayers            => 69
vaultMode             => 0
pvp                   => 2
playerPause           => 0
oneParty              => 0
elc                   => 0
ilr                   => 0
xp                    => 0
moduleNameSize        => 7
moduleName            => v4_EE16
```
