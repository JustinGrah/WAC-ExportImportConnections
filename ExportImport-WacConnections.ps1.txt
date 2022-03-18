#================================
$WacSourceServer = "azs-mgmt"
$WacDestinServer = "azs-wac-demo"
$TagValue = "Automatically_Migrated"
#================================


#Ignore self signed certificates
add-type @"
    using System.Net;
    using System.Security.Cryptography.X509Certificates;
    public class TrustAllCertsPolicy : ICertificatePolicy {
        public bool CheckValidationResult(
            ServicePoint srvPoint, X509Certificate certificate,
            WebRequest request, int certificateProblem) {
            return true;
        }
    }
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Ssl3, [Net.SecurityProtocolType]::Tls, [Net.SecurityProtocolType]::Tls11, [Net.SecurityProtocolType]::Tls12

#Retrieve connection cfg  from source wac
$SourceConnections = Invoke-RestMethod -Method Get -Uri ("https://" + $WacSourceServer + "/api/connections") -ErrorAction SilentlyContinue -UseDefaultCredentials

if($null -ne $SourceConnections) {
    foreach($Connection in $SourceConnections.Value) {
        $ConnectionProperties = $Connection.Properties
        if($null -ne $ConnectionProperties.tags) {
            $ConnectionProperties.tags += $TagValue
        } else {
            Add-Member -InputObject $ConnectionProperties -NotePropertyName "tags" -NotePropertyValue @($TagValue)
        } 
        $ConnectionProperties = ConvertTo-JSON -InputObject $ConnectionProperties
        $Body = "[" + $ConnectionProperties + "]"
        Invoke-RestMethod -Method Put -Uri ("https://" + $WacDestinServer + "/api/connections") -Body $Body -ErrorAction SilentlyContinue -UseDefaultCredentials
    }
}
