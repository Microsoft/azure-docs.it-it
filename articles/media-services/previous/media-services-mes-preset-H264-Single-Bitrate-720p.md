---
title: Media Encoder Standard a bitrate singolo H.264 con risoluzione 720p preimpostato - Azure | Documentazione Microsoft
description: Questo articolo fornisce una panoramica del set di impostazioni dell'attività Media Encoder Standard "H264 a bitrate singolo 720p".
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: f66da66c-9f21-441d-8038-51e3094e9307
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: e48f0c614ba7a7b4199e6ddf0050de0ae7db063f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74886830"
---
# <a name="h264-single-bitrate-720p"></a>H264 Single Bitrate 720p
`Media Encoder Standard` definisce un set di impostazioni di codifica che è possibile usare per la creazione di processi di codifica. È possibile usare un `preset name` per specificare il formato in cui codificare il file multimediale oppure creare set di impostazioni basati su JSON o XML personalizzati, con codifica UTF-8 o UTF-16. Dopodiché, occorre trasmettere il set di impostazioni personalizzato al codificatore. Per un elenco di tutti i nomi dei set di impostazioni supportati dal codificatore `Media Encoder Standard`, vedere [Task Presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Set di impostazioni di attività per Media Encoder Standard).  
  
 Questo argomento illustra il set di impostazioni `H264 Single Bitrate 720p` nei formati XML e JSON.  
  
 Questo set di impostazioni genera un file MP4 con una velocità in bit di 4.500 kbps e audio AAC stereo. Per informazioni dettagliate su profilo, velocità in bit, frequenza di campionamento e così via di questo set di impostazioni, esaminare il codice XML o JSON definito di seguito. Per informazioni sul significato di ogni elemento in questi set di impostazioni e sui valori possibili per ciascuno, vedere lo [schema di Media Encoder Standard](media-services-mes-schema.md).  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>4500</Bitrate>  
          <Width>1280</Width>  
          <Height>720</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>4500</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>128</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 4500,  
          "MaxBitrate": 4500,  
          "BufferWindow": "00:00:05",  
          "Width": 1280,  
          "Height": 720,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "AACLC",  
      "Channels": 2,  
      "SamplingRate": 48000,  
      "Bitrate": 128,  
      "Type": "AACAudio"  
    }  
  ],  
  "Outputs": [  
    {  
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
      "Format": {  
        "Type": "MP4Format"  
      }  
    }  
  ]  
}  
```
