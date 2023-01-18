## Mistake

Running `nixos-collect-garbage -d` to clear space on `boot/eft/nixos`.

## Example

After months of many new generations generated from `nixos-rebuild switch`, it is ran again but results in the following error:
```
[sudo] password for evan:
ignoring default profile in the list of boot entries because of the following error:
[Errno 28] No space left on device: '/nix/store/dns2pq3b3bslfiz921zmwx4497xvi58w-initrd-linux-6.1.6/initrd' -> '/boot/efi/nixos/dns2pq3b3bslfiz921zmwx4497xvi58w-initrd-linux-6.1.6-initrd.efi'
activating the configuration...
```

## Solution

Running the following fixes the issue by deleting all previous generations. This should not be an issue with you are actively pushing new commits to your nixos config to a remote repository.
```
sudo nix-collect-garbage -d
```

Alternatively, you can delete only generation older than a certain measurement of time like the following:
```
sudo nix-collect-garbage --delete-older-than 30d
```

