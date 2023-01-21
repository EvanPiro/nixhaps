## Mistake

I've added a flake to `environment.systemPackages` in my nixos `configuration.nix` but am getting a `command not found` when I try to run the exposed package binary in my shell due to not specifying the full path to the actual package.

## Example

I've added my flake app called "`md`" to my system's `flake.nix` inputs, so it can be loaded it into my `configuration.nix`:

```nix
{
  inputs = {
    nixos-hardware.url = "github:nixos/nixos-hardware";
    nixpkgs.url = "github:EvanPiro/nixpkgs/signage";
    md.url = "github:EvanPiro/md";
  };
  outputs = {
    self,
    nixpkgs,
    nixos-hardware,
    md,
  } @ inputs: let
    system = "x86_64-linux";
  in {
    nixosConfigurations.tower = nixpkgs.lib.nixosSystem {
      inherit system;
      modules = [
        {
          _module.args = {
            inherit inputs;
          };
        }
        ./configuration.nix
        nixos-hardware.nixosModules.common-cpu-amd
        nixos-hardware.nixosModules.common-gpu-amd
      ];
    };
  };
}
```

I've updated my `configuration.nix` to include the `md` flake:

```nix
{
  # ...
  md,
  ...
}: {
  # ...
  environment.systemPackages = with pkgs; [
    # ...
	  md
	  # ...
  ];
  # ...
}
```

When I try to rebuild my sysetm with the new config I get:

```
error: attribute 'md' missing
```

I then realize I need to load the `md` flake as it's own attribute in the modules attribute in the args of `nixosSystem` in my system `flake.nix`.

```
# ...
nixosConfigurations.tower = nixpkgs.lib.nixosSystem {
  inherit system;
  modules = [
    {
      _module.args = {
        inherit inputs md;
      };
    }
	];
}
```

The system builds but when I run `md`, which I expect to be available, I get the following:

```
[evan@tower:~/Repos/nixos-config]$ md
md: command not found
```

## Solution

The full path to the command `md` is not just `md` but `md.packages.${system}.default` and can be set in the `flake.nix` where the module args are being loaded:

```
# ...
nixosConfigurations.tower = nixpkgs.lib.nixosSystem {
  inherit system;
  modules = [
    {
      _module.args = {
        inherit inputs;
				md = md.packages.${system}.default;
      };
    }
	];
}
```
