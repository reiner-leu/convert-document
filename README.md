# convert-document

A docker container environment to bundle the execution of `LibreOffice` to convert documents of various types (such as Word, OpenDocument, etc.) to PDF. An instance of `LibreOffice` will be run in the background, and controlled via a local socket (i.e. the UNO protocol).

## Modifications in this fork
    * Update requirements.txt to use recent libraries
    * Added log messages within endpoint functions

## Usage

This service is intended for use exclusively as a docker container. While it may be possible to
run this application stand-alone, this is not recommended. 

```shell
Get the source code
docker build -t <name of choise> .
if intended to be used as a local container (all logging will be printed on console):
docker run -p 3000:3000 -ti <name of choise> 
or
docker run -p 3000:3000 -d <name of choise>
Remarks: 
  --env CONVERTER_METHOD=unoconv  # is default, any other value seems to lead to a single libreoffice process being started on every call
```

Once the service has initialised, files can be sent to the `/convert` endpoint, and a PDF version
will be returned as a download:

```shell
curl -o out.pdf -F format=pdf -F 'file=@mydoc.doc' http://localhost:3000/convert
A quick check can be done with ./fixtures/agreement.docx as input
```

## Development

To build, run:

```shell
docker build --rm -t alephdata/convert-document .
```

To get a development shell:

```shell
make shell
```

Forced restart

```shell
make build && docker-compose -f docker-compose.dev.yml stop convert-document && docker-compose -f docker-compose.dev.yml up -d convert-document
```

## License

MIT, see `LICENSE`.


## Troubleshooting
(Not sure if this still applies:)
* `LibreOffice` keeps crashing on startup with `Fatal exception: Signal 11`

If [AppArmor](https://help.ubuntu.com/community/AppArmor) is running on the host machine, it may be blocking `LibreOffice` from starting up.
Try disabling the `AppArmor` profiles related to `LibreOffice` by following these instructions: [https://askubuntu.com/a/1214363](https://askubuntu.com/a/1214363)
