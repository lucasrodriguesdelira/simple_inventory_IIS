pipeline{
    agent {label 'Windows'}

	 parameters {
		text(name: 'SERVIDOR', defaultValue: '', description: 'Informe os Servidores para extração')
        }
   
    stages{
		stage('Inventario Middleware'){
		    steps{powershell '''$env:SERVIDOR > "ListServers.txt"
            $servers = Get-Content "ListServers.txt"

            ## Criação do HTML header ##
            "<!DOCTYPE html>
            <HTML>
            <div align=""center"">
            <body>
            <font color =""black"" face=""Microsoft Tai le""><H2> INVENTARIO MIDDLEWARE </H2>" > index.html

            ## Criação dos titulos das colunas em HTML ##
            "<Table border=1 cellpadding=0 cellspacing=0>
            <TR bgcolor=red align=center>
            <TD><FONT COLOR=""white""><B>&nbsp; HOSTNAME &nbsp</B></TD>
            <TD><FONT COLOR=""white""><B>&nbsp; SISTEMA OPERACIONAL &nbsp</B></TD>
            <TD><FONT COLOR=""white""><B>&nbsp; IP &nbsp</B></TD>
            <TD><FONT COLOR=""white""><B>&nbsp; FERRAMENTA &nbsp</B></TD>
            <TD><FONT COLOR=""white""><B>&nbsp; SITES &nbsp</B></TD>
            <TD><FONT COLOR=""white""><B>&nbsp; APPLICATIONS POOL &nbsp</B></TD>
            <TD><FONT COLOR=""white""><B>&nbsp; APPLICATIONS &nbsp</B></TD>
            <TD><FONT COLOR=""white""><B>&nbsp; VDIR &nbsp</B></TD>
            </TR>" >> index.html

            foreach($server in $servers){
                $vIIS = Invoke-Command -ComputerName $server -scriptblock {(get-itemproperty HKLM:\\SOFTWARE\\Microsoft\\InetStp\\ | Select-Object setupstring | Format-Wide | Out-String).Trim()}
                $SO = Get-WmiObject -Class Win32_OperatingSystem -ComputerName $server | ForEach-Object -MemberName Caption
                $ip = (Test-Connection -ComputerName $server -Count 1 | Select-Object IPV4Address | Format-Wide | Out-String).Trim()

                ########################################## ApplicationPool ##########################################
                $lista_pool = invoke-command -computername $server -scriptblock {C:\\Windows\\System32\\inetsrv\\.\\appcmd.exe list apppool} | ForEach-Object{$_.Split(\'"\')[1];}
                $count_pool = $lista_pool.Count

                $html ="<!DOCTYPE>
                <HTML><div align=""center"">
                <body>
                <font color =""black"" face=""Microsoft Tai le""><H2> Applications Pools </H2>"

                $html +="<Table border=1 cellpadding=0 cellspacing=0>
                <TR bgcolor=red align=center>
                <TD><FONT COLOR=""white""><B>&nbsp; Servidor &nbsp</B></TD>
                <TD><FONT COLOR=""white""><B>&nbsp; Resultados encontrados &nbsp</B></TD>
                </TR>"

                foreach ($pool in $lista_pool){
                    $html += "<TR>
                    <TD align=center>&nbsp;$server&nbsp</TD>
                    <TD align=center>&nbsp;$pool&nbsp</TD>
                    </TR>"
                }

                $html | Out-File .\\pool\\pool_$server.html

                ########################################## Site ##########################################
                $lista_site = invoke-command -computername $server -scriptblock {C:\\Windows\\System32\\inetsrv\\.\\appcmd.exe list site} | ForEach-Object{$_.Split(\'"\')[1];}
                $count_site = $lista_site.Count

                $html ="<!DOCTYPE>
                <HTML><div align=""center"">
                <body>
                <font color =""black"" face=""Microsoft Tai le""><H2> Sites </H2>"

                $html +="<Table border=1 cellpadding=0 cellspacing=0>
                <TR bgcolor=red align=center>
                <TD><FONT COLOR=""white""><B>&nbsp; Servidor &nbsp</B></TD>
                <TD><FONT COLOR=""white""><B>&nbsp; Resultados encontrados &nbsp</B></TD>
                </TR>"

                foreach ($site in $lista_site){
                    $html += "<TR>
                    <TD align=center>&nbsp;$server&nbsp</TD>
                    <TD align=center>&nbsp;$site&nbsp</TD>
                    </TR>"
                }
                                    
                $html | Out-File .\\site\\site_$server.html

                ########################################## Application ##########################################
                $lista_app = invoke-command -computername $server -scriptblock {C:\\Windows\\System32\\inetsrv\\.\\appcmd.exe list app} | ForEach-Object{$_.Split(\'"\')[1];}
                $count_app = $lista_app.Count

                $html ="<!DOCTYPE>
                <HTML><div align=""center"">
                <body>
                <font color =""black"" face=""Microsoft Tai le""><H2> Application </H2>"
                
                $html +="<Table border=1 cellpadding=0 cellspacing=0>
                <TR bgcolor=red align=center>
                <TD><FONT COLOR=""white""><B>&nbsp; Servidor &nbsp</B></TD>
                <TD><FONT COLOR=""white""><B>&nbsp; Resultados encontrados &nbsp</B></TD>
                </TR>"

                foreach ($app in $lista_app){
                    $html += "<TR>
                    <TD align=center>&nbsp;$server&nbsp</TD>
                    <TD align=center>&nbsp;$app&nbsp</TD>
                    </TR>"
                }
                    
                $html | Out-File .\\app\\app_$server.html

                ########################################## Diretorio Virtual ##########################################
                $lista_vdir = invoke-command -computername $server -scriptblock {C:\\Windows\\System32\\inetsrv\\.\\appcmd.exe list vdir /config} | ForEach-Object{$_.Split(\'"\')[3];}
                $count_vdir = $lista_vdir.Count

                $html ="<!DOCTYPE>
                <HTML><div align=""center"">
                <body>
                <font color =""black"" face=""Microsoft Tai le""><H2> Diretorios Virtuais </H2>"

                $html +="<Table border=1 cellpadding=0 cellspacing=0>
                <TR bgcolor=red align=center>
                <TD><FONT COLOR=""white""><B>&nbsp; Servidor &nbsp</B></TD>
                <TD><FONT COLOR=""white""><B>&nbsp; Resultados encontrados &nbsp</B></TD>
                </TR>"

                foreach ($vdir in $lista_vdir){
                    $html += "<TR>
                    <TD align=center>&nbsp;$server&nbsp</TD>
                    <TD align=center>&nbsp;$vdir&nbsp</TD>
                    </TR>"
                }

                $html | Out-File .\\vdir\\vdir_$server.html

                ########################################## INDEX ##########################################
                "<TD align=center>&nbsp; $server &nbsp</TD>
                <TD align=center>&nbsp; $SO &nbsp</TD>
                <TD align=center>&nbsp; $ip &nbsp</TD>
                <TD align=center>&nbsp; $vIIS &nbsp</TD>
                <TD align=center><a href="".\\site\\site_$server.html"">$count_site</TD>
                <TD align=center><a href="".\\pool\\pool_$server.html"">$count_pool</TD>
                <TD align=center><a href="".\\app\\app_$server.html"">$count_app</TD>
                <TD align=center><a href="".\\vdir\\vdir_$server.html"">$count_vdir</TD>
                </TR>" >> index.html
            }'''
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '', reportFiles: 'index.html', reportName: 'Report inventario', reportTitles: ''])
            }
        }
    }
}