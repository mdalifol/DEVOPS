# FFMPEG

## Documentaion officielle
[ffmpeg](https://ffmpeg.org/)
[commandes utiles](
  * https://digitalfortress.tech/tricks/encode-videos-with-ffmpeg/
  * https://dev.to/benjaminblack/use-ffmpeg-to-compress-and-convert-videos-458l
  )

## Installation

[explications et commandes](https://emirchouchane.com/tutoriel-ffmpeg/)
[git pour installation complète](https://gist.github.com/Piasy/b5dfd5c048eb69d1b91719988c0325d8)

* installation ffmpeg avec toutes les options
  `brew install ffmpeg $(brew options ffmpeg | grep -vE '\s' | grep -- '--with-' | tr '\n' ' ')`

## Utilisation

### Connaitre les infos d'une vidéos

  `ffmpeg -i <nomVideo>`

### Convertir une vidéo dans un autre format

  `ffmpeg -i <nomVideoAConvertir.nomFormat> <nomVideoConvertie.nouveauFormat>`


### Changer le format d'une vidéo

  `ffmpeg -i <nomVideoAConvertir.nomFormat> -acodec copy -vcodec copy <nomVideoConvertie.nouveauFormat>`

#### Convertir un.mkv en .avi
**un filtre supplémentaire est nécessaire**

  `ffmpeg -i <nomVideoAConvertir.nomFormat> -acodec copy -vcodec copy -bsf:v h264_mp4toannexb <nomVideoConvertie.nouveauFormat>`

### CHANGER LA TAILLE D'UNE VIDEO SANS PERDRE LA QUALITE (non)
#### h264
  `ffmpeg -i inputFile.mkv -c:v libx264 -preset ultrafast -crf 0 outputFile.mkv`

__crf__: option avec "0" pour ne pas perdre en qualité


####  h265
  `ffmpeg -i inputFile.mkv -c:v libx265 -crf 0 outputFile.mkv`
