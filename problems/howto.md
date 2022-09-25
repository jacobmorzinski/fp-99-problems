how to download:

```powershell
mkdir v1
cd v1
$v1 = Invoke-WebRequest "https://www.ic.unicamp.br/~meidanis/courses/mc336/2009s2/prolog/problemas/"
$v1.Content | Out-File -Encoding utf8NoBOM v1.html

pandoc v1.html -o v1.md

($v1.Links) | Where-Object { $_.href -match "p\d\d.pl" } | ForEach-Object { Invoke-WebRequest ("https://www.ic.unicamp.br/~meidanis/courses/mc336/2009s2/prolog/problemas/" + $_.href) | Set-Variable r ; [System.Text.Encoding]::ASCII.GetString($r.Content) | Out-File -Encoding utf8NoBOM $_.href }

($v1.Links) | Where-Object { $_.href -notmatch "p\d\d.pl" } | Set-Variable otherlinks

$otherlinks | Where-Object { $_.href -match "p\d\d\S*.(pl|dat)" } | ForEach-Object { Invoke-WebRequest ("https://www.ic.unicamp.br/~meidanis/courses/mc336/2009s2/prolog/problemas/" + $_.href) | Set-Variable r ; [System.Text.Encoding]::ASCII.GetString($r.Content) | Out-File -Encoding utf8NoBOM $_.href }

$otherlinks | Where-Object { $_.href -match "p\d\d\S*.(txt)" } | ForEach-Object { Invoke-WebRequest ("https://www.ic.unicamp.br/~meidanis/courses/mc336/2009s2/prolog/problemas/" + $_.href) | Set-Variable r ; $r.Content | Out-File -Encoding utf8NoBOM $_.href }

# images

($v1.Images) | ForEach-Object { $r = Invoke-WebRequest ("https://www.ic.unicamp.br/~meidanis/courses/mc336/2009s2/prolog/problemas/" + $_.src) -OutFile $_.src }

```
