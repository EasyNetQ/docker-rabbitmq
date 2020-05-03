FROM mcr.microsoft.com/windows/servercore:ltsc2019

ENV ERLANG_HOME="c:\erlang" \
    ERLANG_VERSION="21.3" \
    RABBITMQ_VERSION="3.7.25" \
    RABBITMQ_BASE="c:\rabbitmq" \
    RABBITMQ_CONFIG_FILE="c:\rabbitmq\rmqsettings"

SHELL ["powershell", "-Command"]

EXPOSE 1883 4369 5671 5672 8883 15671 15672 15674 15675 25672 61613 61614

RUN $erlangInstaller = Join-Path -Path $env:Temp -ChildPath 'otp_win64.exe'; \
    Invoke-WebRequest -UseBasicParsing -Uri "http://erlang.org/download/otp_win64_$env:ERLANG_VERSION.exe" -OutFile $erlangInstaller; \
    Unblock-File -Path $erlangInstaller; \
    Start-Process -NoNewWindow -Wait -FilePath $erlangInstaller -ArgumentList /S, /D=$env:ERLANG_HOME; \
    Remove-Item -Force -Path $erlangInstaller; \
    $rabbitZip = Join-Path -Path $env:Temp -ChildPath 'rabbitmq.zip'; \
    Invoke-WebRequest -UseBasicParsing -Uri "http://dl.bintray.com/rabbitmq/all/rabbitmq-server/$env:RABBITMQ_VERSION/rabbitmq-server-windows-$env:RABBITMQ_VERSION.zip" -OutFile $rabbitZip; \
    Unblock-File -Path $rabbitZip; \
    Expand-Archive -Path $rabbitZip -DestinationPath 'c:\'; \
    Remove-Item -Force -Path $rabbitZip; \
    Rename-Item -Path "c:\rabbitmq_server-$env:RABBITMQ_VERSION" -NewName 'c:\rabbitmq'; \
    Remove-Item -Force -Path C:\rabbitmq\*.txt; \
    $utf8NoBomEncoding = New-Object System.Text.UTF8Encoding $False; \
    [System.IO.File]::WriteAllText('C:\rabbitmq\rmqsettings.config', '[{rabbit,[{loopback_users, []}]}].', $utf8NoBomEncoding); \
    Invoke-WebRequest -UseBasicParsing -Uri "https://github.com/rabbitmq/rabbitmq-delayed-message-exchange/releases/download/v3.8.0/rabbitmq_delayed_message_exchange-3.8.0.ez" -OutFile "C:\rabbitmq\plugins\rabbitmq_delayed_message_exchange-3.8.0.ez"; \
    C:\rabbitmq\sbin\rabbitmq-plugins.bat enable --offline rabbitmq_management rabbitmq_federation rabbitmq_federation_management rabbitmq_delayed_message_exchange

CMD c:\rabbitmq\sbin\rabbitmq-server.bat