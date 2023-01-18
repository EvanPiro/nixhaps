## Mistake

I've added my package to a clone of [`nixpkgs`](https://github.com/NixOS/nixpkgs) but it's not showing up when I try to build it.

## Example

I've added `pkgs/development/tools/my-pkg/default.nix`. When I build it I get the follow error:

```
[evan@tower:~/Repos/nixpkgs]$ nix-build -A my-pkg
error: attribute 'my-pkg' in selection path 'my-pkg' not found
```

## Solution

The package needs to be added to the attribute set in `pkgs/top-level/all-packages.nix` to become visible `nix-build`, like the following:

```nix
{
# ...
  my-pkg = callPackage ../development/tools/my-pkg { };
# ...
}
```

