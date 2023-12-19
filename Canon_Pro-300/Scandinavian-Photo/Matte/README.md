# Scandinavian Photo Matte

230G

## Commands

```
targen -v -d2 -G -g128 -f1120 XP_960-satin-professional
printtarg -v2 -ii1 -T300 -p A4R -m0 -M4 -P -L XP_960-satin-professional
chartread -v -H -T 0.4 XP_960-satin-professional
colprof -v -qu -r0.7 -S ..\..\..\AdobeRGB1998.icc -cmt -dpp -D "Epson XP-960 / ScandinavianPhoto Satin Professional" XP_960-satin-professional
profcheck ...
```