This a sub-repo of [Neard project](https://github.com/crazy-max/neard) involving phpMyAdmin app bundles.

## Installation

* Download and install [Neard](https://github.com/crazy-max/neard).
* If you already have installed Neard, stop it.
* Download [a phpMyAdmin bundle](#download).
* Extract archive in `neard\apps\phpmyadmin\`. Directory structure example :

```
[-] neard
 | [-] apps
 |  | [-] phpmyadmin 
 |  |  | [-] phpmyadmin4.0.4
 |  |     | neard.conf
 ```

* Edit the `neard.conf` file and replace the key `phpmyadminVersion` with the correct version.
* Edit the `alias/phpmyadmin.conf` file and replace the lines with the correct version. 
* Start Neard.

## Download

![](https://raw.github.com/crazy-max/neard-app-phpmyadmin/master/img/star-20160403.png) : Default bundle on Neard.

|                      | phpMyAdmin release date | Neard release | Download |
| ---------------------|:-----------------------:|:-------------:|:--------:|
| **phpMyAdmin 4.0.4** ![](https://raw.github.com/crazy-max/neard-app-phpmyadmin/master/img/star-20160403.png) | 2013/06/01 | [r1](https://github.com/crazy-max/neard-app-phpmyadmin/releases/tag/r1) | [neard-phpmyadmin-4.0.4-r1.zip](https://github.com/crazy-max/neard-app-phpmyadmin/releases/download/r1/neard-phpmyadmin-4.0.4-r1.zip) |

## Issues

Issues must be reported on [Neard repository](https://github.com/crazy-max/neard/issues).<br />
Please read [Found a bug?](https://github.com/crazy-max/neard#found-a-bug) section before reporting an issue.
