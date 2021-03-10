# Cartoonizer-vb.net
Convert Photos into Cartoon Like Images/ Cartoonize images using multi-pass bilateral filter and edge detection

[Forked](https://www.codeproject.com/Articles/143355/Cartoonizer-Convert-Photos-into-Cartoon-Like-Image)

## Introduction
This allows you to apply a painting/cartoon effect to an image by using a bilateral filter followed by edge detection.

## Background
This is not my original work. This is a port from VB6 to VB.NET. After extensively searching the net for a decent cartoon effect, I found this VB6 project by Roberto Mior and wanted to preserve it and protect it from going extinct due to it being written in VB6.

## Using the Code
Larger images than 600x800 and larger Radius settings will make the filters process more pixels which in turn makes it slower.

The main workflow is:

`GetBytesFromImage --> MultiPassBilateralFilter --> SobelEdgeDetectionFilter --> LuminanceSegmentationFIlter --> PutBytesIntoImage`

All of the algorithms used here can be found easily on the internet.

### Example workflow:

``` vb.net
  Private Sub btCartoonize_Click(ByVal sender As System.Object, _
	ByVal e As System.EventArgs) Handles btCartoonize.Click
    PictureBox1.Image = mBitmap
    PictureBox1.Refresh()
    Cartoonize()
  End Sub

  Private Sub DoPreEffect()
    Select Case cbPreEffect.SelectedIndex
      Case 0
      Case 1
        FX.MagneKleverHistogramEQU(0.3)
      Case 2
        FX.MagneKleverExposure(tbExposure.Value)
      Case 3
        FX.MagneKleverBCS(tbBrightness.Value, tbContrast.Value, tbSaturation.Value)
    End Select
  End Sub

  Private Sub DoContour()
    If tbContourAmount.Value > 0 Then
      Select Case cbContourMethod.SelectedIndex
        Case 0
          FX.zEFF_Contour(tbContourAmount.Value, tbLumHue.Value / 100)
        Case 1
          FX.zEFF_ContourbyDOG(tbContourAmount.Value, tbContourThreshold.Value / 1000)
      End Select
    End If
  End Sub

  Private Sub ApplyContour()
    If tbContourAmount.Value > 0 Then
      FX.zEFF_Contour_Apply()
    End If
  End Sub

  Private Sub DoBilateral()
    FX.zEFF_BilateralFilter(tbRadius.Value, tbSpatialIntensity.Value / 10000, _
	tbSpatialSigma.Value / 100, tbIterations.Value, cbIntensityMode.SelectedIndex, _
	cbColorMode.SelectedIndex = 0)
  End Sub

  Private Sub DoQuantizeLuminance()
    FX.zEFF_QuantizeLuminance(tbSegments.Value, tbPresence.Value / 100, _
	tbRadius.Value, False)
  End Sub

  'Private Sub InitDomains()
  '  FX.zInit_IntensityDomain(tbSpatialIntensity.Value / 10000, 
  '  cbIntensityMode.SelectedIndex)
  '  FX.zInit_SpatialDomain(tbSpatialSigma.Value / 100)
  'End Sub

  Public Sub Cartoonize()
    Dim bm As Bitmap = mBitmap.Clone()
    FX.zSet_Source(bm)
    DoPreEffect()
    'Uncomment this if you want to call this without the UI
    'InitDomains
    DoBilateral()
    DoContour()
    DoQuantizeLuminance()
    ApplyContour()

    FX.zGet_Effect(bm)
    PictureBox1.Image = bm
    PictureBox1.Refresh()
    FX_PercDONE("", 0, 0)
  End Sub

```

## Points of Interest
The main challenge of porting the code was to be aware of the effect of implicit casting in the VB6 code. The VB6 code is faster than the VB.NET code. To speed this up, the effect processing will need to be written in C++ to process the byte arrays.

## History
* 6th January, 2011: 1.0 - Initial revision from VB6 port
* 18th April, 2011: Fixed bitmap byte access code - fix provided by gilchinger (bauer)


> This article, along with any associated source code and files, is licensed under The GNU General Public License (GPLv3)
