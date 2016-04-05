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
 |  |  | [-] phpmyadmin4p1
 |  |     | neard.conf
 ```

* Edit the `neard.conf` file and replace the key `phpmyadminVersion` with the correct version. (eg. `phpmyadminVersion="4.0.1"`, `phpmyadminVersion="4p1"`)
* Edit the `alias/phpmyadmin.conf` file and replace the lines with the correct version. (Not necessary since Neard 1.0.18)
* Start Neard.

## Download

![](https://raw.github.com/crazy-max/neard-app-phpmyadmin/master/img/star-20160403.png) : Default bundle on Neard.

|                      | phpMyAdmin pack releases | phpMyAdmin release date | Neard release | Download |
| ---------------------|:------------------------:|:-----------------------:|:-------------:|:--------:|
| **phpMyAdmin 4.0.4** ![](https://raw.github.com/crazy-max/neard-app-phpmyadmin/master/img/star-20160403.png) | N/A | 2013/06/17 | [r1](https://github.com/crazy-max/neard-app-phpmyadmin/releases/tag/r1) | [neard-phpmyadmin-4.0.4-r1.zip](https://github.com/crazy-max/neard-app-phpmyadmin/releases/download/r1/neard-phpmyadmin-4.0.4-r1.zip) |
| **phpMyAdmin 4p1**  | 4.0.10.12<br />4.4.15.2<br />4.5.3.1 | 2015/12/25<br />2015/12/25<br />2015/12/25 | [r2](https://github.com/crazy-max/neard-app-phpmyadmin/releases/tag/r2) | [neard-phpmyadmin-4p1-r2.zip](https://github.com/crazy-max/neard-app-phpmyadmin/releases/download/r2/neard-phpmyadmin-4p1-r2.zip) |

## Issues

Issues must be reported on [Neard repository](https://github.com/crazy-max/neard/issues).<br />
Please read [Found a bug?](https://github.com/crazy-max/neard#found-a-bug) section before reporting an issue.
