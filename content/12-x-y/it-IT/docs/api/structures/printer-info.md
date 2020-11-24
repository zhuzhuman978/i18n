# Oggetto PrinterInfo

* `name` String - the name of the printer as understood by the OS.
* `displayName` String - the name of the printer as shown in Print Preview.
* `description` String - a longer description of the printer's type.
* `status` Number - the current status of the printer.
* `isDefault` Boolean - whether or not a given printer is set as the default printer on the OS.
* `options` Object - an object containing a variable number of platform-specific printer information.

Il numero rappresentato dallo `stato` significa diverse cose su piattaforme diverse: su Windows i suoi potenziali valori si possono trovare [qui](https://docs.microsoft.com/en-us/windows/win32/printdocs/printer-info-2) e su Linux e MaxOS si possono trovare [qui](https://www.cups.org/doc/cupspm.html).

## Esempio

Di seguito un esempio di alcuni parametri addizionali che possono essere impostati e che potrebbero differenziarsi tra le varie piattaforme.

```javascript
{
  name: 'Austin_4th_Floor_Printer___C02XK13BJHD4',
  displayName: 'Austin 4th Floor Printer @ C02XK13BJHD4',
  description: 'TOSHIBA ColorMFP',
  status: 3,
  isDefault: false,
  options: {
    copies: '1',
    'device-uri': 'dnssd://Austin%204th%20Floor%20Printer%20%40%20C02XK13BJHD4._ipps._tcp.local./?uuid=71687f1e-1147-3274-6674-22de61b110bd',
    finishings: '3',
    'job-cancel-after': '10800',
    'job-hold-until': 'no-hold',
    'job-priority': '50',
    'job-sheets': 'none,none',
    'marker-change-time': '0',
    'number-up': '1',
    'printer-commands': 'ReportLevels,PrintSelfTestPage,com.toshiba.ColourProfiles.update,com.toshiba.EFiling.update,com.toshiba.EFiling.checkPassword',
    'printer-info': 'Austin 4th Floor Printer @ C02XK13BJHD4',
    'printer-is-accepting-jobs': 'true',
    'printer-is-shared': 'false',
    'printer-is-temporary': 'false',
    'printer-location': '',
    'printer-make-and-model': 'TOSHIBA ColorMFP',
    'printer-state': '3',
    'printer-state-change-time': '1573472937',
    'printer-state-reasons': 'offline-report,com.toshiba.snmp.failed',
    'printer-type': '10531038',
    'printer-uri-supported': 'ipp://localhost/printers/Austin_4th_Floor_Printer___C02XK13BJHD4',
    system_driverinfo: 'T'
  }
}
```