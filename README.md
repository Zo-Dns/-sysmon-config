English is below
  sysmonconfig-export.xml (edit) SwiftOnSecurity التعديل على نسخة 
-----------------------------
number 4.90  نسخة المخطط المعدلة 
---------------------------------
  Clipboard - Event 24 تفعيل مراقبة النسخ واللصق
-----------------------------
  Process Tampering - Event 25 تفعيل مراقبة التلاعب بالعمليات
------------------------------------------------

IBM registry events https://www.ibm.com/docs/en/qradar-common?topic=endpoint-configure-windows-endpoints اضفنا قواعد IBM
-----------------------
with our 3 rls اضفنا قواعدنا الخاصة ايضا معها 
   
    ms-settings\shell\open\command Hijacks fodhelper.exe via ms-settings handler, no UAC prompt shown
    Mscfile\shell\open\command  Hijacks eventvwr.exe auto-elevation via Mscfile handler
    fodhelper  Direct modification of fodhelper registry key for privilege escalation
    --------------------------------------------------
    
Add event 27 اضافة الحدث
27 Execution Prevention: تمنع هذه القاعدة تشغيل أي ملفات تنفيذية داخل المجلدات المؤقتة (Temp) لتقليل فرص عمل البرمجيات الخبيثة.

 F-Name C:\Windows\Temp\ and \AppData\Local\Temp\

 27 Exclusion Policies: تستثني هذه القاعدة برامج النظام والعمليات الموثوقة من هذا المنع لضمان استقرار أداء ويندوز وتطبيقاته الرسمية.

 in the code بداخل الكود معلومات كاملة عن الاستنثاء لهذه القاعدة والحدث

---------------
English 
Sysmon Configuration Documentation

Base Configuration: SwiftOnSecurity/sysmon-config (Version 74)
Modified Schema Version: 4.90
1. Monitoring Enhancements

    Clipboard Monitoring (Event ID 24): Added inclusion rules to monitor clipboard activity, specifically targeting wscript.exe, cscript.exe, powershell.exe, and rdpclip.exe to detect potential data exfiltration or suspicious injection techniques.

    Process Tampering (Event ID 25): Enabled monitoring for process image changes initiated by external sources. Includes exclusions for legitimate Edge application updates to minimize noise.

2. IBM QRadar Integration & Security Hardening

Aligned the registry monitoring rules with IBM QRadar Endpoint Content Extension requirements to detect UAC bypass techniques.

Registry Events (RegistryEvent) Added:

    \Environment\: Monitored to detect unauthorized changes to system/user environment variables.

    \CurVer: Monitored as it can be used to redirect progid handlers.

    \URL Protocol: Monitored to detect associations created to facilitate handler hijacking.

    \ICM\Calibration: Monitored to detect IColorDataProxy auto-elevation abuse.

Custom UAC Bypass Rules Added:

    ms-settings\shell\open\command: Detects fodhelper.exe hijacking via the ms-settings handler (bypassing UAC prompts).

    Mscfile\shell\open\command: Detects eventvwr.exe auto-elevation via the Mscfile handler.

    fodhelper: Detects direct modification of the fodhelper registry key for privilege escalation.

3. Execution Prevention (Event ID 27)

Implemented a high-security execution prevention layer to block malicious droppers in high-risk directories.

    Inclusion Rule (Block): Blocks any executable file (.exe) being written to:

        C:\Windows\Temp\

        \AppData\Local\Temp\

    Exclusion Policy (Allow): To maintain system stability and support legitimate software installation, the following trusted processes and paths are excluded from the block:

        System Installers: msiexec.exe, TrustedInstaller.exe, and wusa.exe (required for Windows updates and security patches).

        Trusted Directories: C:\Program Files\ and C:\Program Files (x86)\ (prevents breaking legitimate software installations).

    Warning: Removing these exclusions will prevent critical Windows updates and software installations from functioning.

Technical Implementation Note

    Schema Version: Updated to 4.90.

    Event Filtering: All new rules are categorized using RuleGroup and onmatch logic to ensure that security alerts are correctly tagged (e.g., RuleName="Alert,UACBypass") for integration with SIEM platforms like QRadar.

This configuration now provides a robust defense-in-depth approach, combining industry-standard logging with targeted blocking of common post-exploitation techniques.
