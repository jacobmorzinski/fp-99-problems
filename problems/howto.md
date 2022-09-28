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

Invoke-WebRequest -Uri  -OutFile prolog-program.gif

[uri[]] $image_uris = @(
    "https://sites.google.com/site/prologsite/_/rsrc/1264864357592/home/prolog-program.gif"
    "https://sites.google.com/site/prologsite/_/rsrc/1264933989828/prolog-problems/4/p64.gif"
    "https://sites.google.com/site/prologsite/_/rsrc/1264934255598/prolog-problems/4/p65.gif"
    "https://sites.google.com/site/prologsite/_/rsrc/1264934330462/prolog-problems/4/p66.gif"
    "https://sites.google.com/site/prologsite/_/rsrc/1264934442609/prolog-problems/4/p67.gif"
    "https://sites.google.com/site/prologsite/_/rsrc/1264946214751/prolog-problems/5/p70.gif"
    "https://sites.google.com/site/prologsite/_/rsrc/1264946557086/prolog-problems/5/p73.png"
    "https://sites.google.com/site/prologsite/_/rsrc/1264948248705/prolog-problems/6/graph1.gif"
    "https://sites.google.com/site/prologsite/_/rsrc/1264948667063/prolog-problems/6/graph2.gif"
    "https://sites.google.com/site/prologsite/_/rsrc/1264948803128/prolog-problems/6/graph3.gif"
    "https://sites.google.com/site/prologsite/_/rsrc/1264949059996/prolog-problems/6/p83.gif"
    "https://sites.google.com/site/prologsite/_/rsrc/1264949163407/prolog-problems/6/p84.gif"
    "https://sites.google.com/site/prologsite/_/rsrc/1264955485190/prolog-problems/7/p92a.gif"
    "https://sites.google.com/site/prologsite/_/rsrc/1264955528417/prolog-problems/7/p92b.gif"
    "https://sites.google.com/site/prologsite/_/rsrc/1264955783053/prolog-problems/7/p96.gif"
    "https://sites.google.com/site/prologsite/_/rsrc/1264956149766/prolog-problems/7/p99.gif"
    "https://sites.google.com/site/prologsite/_/rsrc/1264946557086/prolog-problems/5/p73.png"
)
foreach ($uri in $image_uris) {
    Invoke-WebRequest -Uri $uri -OutFile $uri.Segments[-1]
}

```
