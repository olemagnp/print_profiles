# Commands

```bash
targen -v -d2 -G -g128 -f1120 XP_960-Caonon-MP-101
printtarg -v2 -ii1 -T300 -p A4R -m0 -M4 -P -L XP_960-Caonon-MP-101
chartread -v -H -T 0.4 XP_960-Caonon-MP-101
colprof -v -qu -r0.7 -S ..\..\..\AdobeRGB1998.icc -cmt -dpp -D "Epson XP-960 / Canon MP-101" XP_960-Caonon-MP-101
profcheck ...
```

# Settings in printer
Paper: Premium presentation paper matte
Color-management: None