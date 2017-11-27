# Powershell
# Unzipping the nested zip folders using powershell V2.0
#Base folder from where files to be unzipped

$path = "C:\path"

#7zip exe location
$Zip_exe_Path="c:\Program Files\7-Zip\7z.exe"

$logfile="C:\log.txt"

$ErrorActionPreference = 'Stop'

#Select Zip files 
$source = get-childitem $path -Filter "*.zip" -recurse|where-object {$_.length -gt 0}|Select-Object -ExpandProperty FullName
do{
                
foreach($zip in $source)
{try{
                # get the output location to extract files
                $dir=split-path $zip 
                 $par = (Get-Item $zip).basename
                $out=$dir+"\"+$par
                #$ZipArgs=@("x","-o$out","$zip", "-y")
                $ZipArgs=@("x","-o$out","$zip")

                        $Unzip_Status=(start-process $Zip_exe_Path -argumentlist $ZipArgs -nonewwindow -wait -passthru).exitcode
                        if($Unzip_Status -eq 0)
                        {Move-Item -path $zip -Destination C:\Unzip -Force }
$source = get-childitem $path -Filter "*.zip" -recurse|where-object {$_.length -gt 0}|Select-Object -ExpandProperty FullName
}
Catch{"$_" | out-file $logfile -append}
}

}while ($source.count -gt 0)
