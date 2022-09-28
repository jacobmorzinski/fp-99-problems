how to download:

```powershell
mkdir v1
cd v1
$v1 = Invoke-WebRequest "https://www.ic.unicamp.br/~meidanis/courses/mc336/2009s2/prolog/problemas/"
$v1.Content | Out-File -Encoding utf8NoBOM v1.html

pandoc v1.html -o v1.md -t gfm

($v1.Links) | Where-Object { $_.href -match "p\d\d.pl" } | ForEach-Object { Invoke-WebRequest ("https://www.ic.unicamp.br/~meidanis/courses/mc336/2009s2/prolog/problemas/" + $_.href) | Set-Variable r ; [System.Text.Encoding]::ASCII.GetString($r.Content) | Out-File -Encoding utf8NoBOM $_.href }

($v1.Links) | Where-Object { $_.href -notmatch "p\d\d.pl" } | Set-Variable otherlinks

$otherlinks | Where-Object { $_.href -match "p\d\d\S*.(pl|dat)" } | ForEach-Object { Invoke-WebRequest ("https://www.ic.unicamp.br/~meidanis/courses/mc336/2009s2/prolog/problemas/" + $_.href) | Set-Variable r ; [System.Text.Encoding]::ASCII.GetString($r.Content) | Out-File -Encoding utf8NoBOM $_.href }

$otherlinks | Where-Object { $_.href -match "p\d\d\S*.(txt)" } | ForEach-Object { Invoke-WebRequest ("https://www.ic.unicamp.br/~meidanis/courses/mc336/2009s2/prolog/problemas/" + $_.href) | Set-Variable r ; $r.Content | Out-File -Encoding utf8NoBOM $_.href }

# images

($v1.Images) | ForEach-Object { $r = Invoke-WebRequest ("https://www.ic.unicamp.br/~meidanis/courses/mc336/2009s2/prolog/problemas/" + $_.src) -OutFile $_.src }

```

```powershell
mkdir v2
cd v2

function download-and-convert ($url, $file) {
    $r = Invoke-WebRequest $url
    $c = $r.Content
    $c | Out-File "${file}.html" -Encoding utf8NoBOM
    # This is fragile, but PowerShell Core 7 does not parse html.
    $start = $c.IndexOf('<div dir="ltr">')
    $end = $c.IndexOf('<div id="sites-attachments-container">')
    $c.Substring($start,($end-$start)) | pandoc - -f html-native_divs-native_spans -o "${file}.md" -t gfm
}

download-and-convert "https://sites.google.com/site/prologsite/prolog-problems/1" "1-lists"
download-and-convert "https://sites.google.com/site/prologsite/prolog-problems/2" "2-arithmetic"
download-and-convert "https://sites.google.com/site/prologsite/prolog-problems/3" "3-logic-and-codes"
download-and-convert "https://sites.google.com/site/prologsite/prolog-problems/4" "4-binary-trees"
download-and-convert "https://sites.google.com/site/prologsite/prolog-problems/5" "5-multiway-trees"
download-and-convert "https://sites.google.com/site/prologsite/prolog-problems/6" "6-graphs"
download-and-convert "https://sites.google.com/site/prologsite/prolog-problems/7" "7-miscellaneous"

# download 0-foreward.html by hand, then:
pandoc.exe "0-foreward.html" -f html-native_divs-native_spans -o "0-foreward.md" -t gfm

```
