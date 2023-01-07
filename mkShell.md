## Mistake

Creating a basic flake that uses mkShell without getting the path to mkShell right.

## Example

### Code
```nix
{
  inputs = {
    nixpkgs.url = "github:NixOs/nixpkgs/master";
  };
  outputs = { self, nixpkgs }:
    let system = "x86_64-linux"; in {
      devShells.${system}.default = nixpkgs.mkShell {
        buildInputs = [];
      };
  };
}
```

### Error

```
[evan@tower:~/Repos/simple-node]$ nix develop
error: attribute 'mkShell' missing

       at /nix/store/348nrmhjxfa6g0v37iv4q62d78m2s8yv-source/flake.nix:7:37:

            6|     let system = "x86_64-linux"; in {
            7|       devShells.${system}.default = nixpkgs.mkShell {
             |                                     ^
            8|         buildInputs = [];

```

## Solution

The path to `mkShell` is `nixpkgs.legacyPackages.${system}.mkShell`

