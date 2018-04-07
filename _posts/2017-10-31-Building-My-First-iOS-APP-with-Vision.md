---
layout: post
comments: true
title:  "Building My First iOS APP with Vision!"
date:   2018-3-10
excerpt: "How I built a facial detection app using the power of CoreML"
image: "/images/swift.png"
---

## Background

This is indeed my first time working with iOS. I'd like to thank Udacity for their free online course on iOS development. If you want to get started with iOS development, I'd highly recommend taking a MOOC and practice on the side. It really is a lot of fun.

This app serves as a proof-of-concept on how networking events can be improved. Wouldn't it be nice if there is a way for the event attendees to easily identify the people they want to talk to? We wanted to design an app that the user can use to point at other faces to instantly get more information on the target's current company, job title and connection information. Of course, at the same time we need to follow privacy laws such as the Illinoi's Biometric Privacy Act and restrict the targets to just the people within the networking events. 

{: .image.right}
![workflow](/images/faceology/workflow.png)

The logic flow of this app is as follows: the user login with LinkedIn and scans a QR code to register for an event. The app then sends the user's LinkedIn profile information and the event ID to the backend Flask powered server, which adds the user to a local database. The user can then start scanning faces. The app uses the built in facial detection to capture faces, then sends it back to the server to perform one-hot matchings. If there is a match, the target face will be displayed at the bottom of screen. The user can then tap it to get more professional information on the target.

## Update

So here I want to talk about my experience iwth iOS development and what I learned. Skip this if you just want to follow the build guide. 

### On Swift

Swift is an interesting language to learn for sure. This is my first iOS app so I don't know anything about Objective-c or Swift before 4.0, but I believe it is putting more focus on usability and type checking. Mainly, the first thing I noticed about Swift was the use of `!` and `?` and the difference between `let` and `var`. In swift a variable can be defined like this 

{% highlight swift %}
private var imageView: UIImageView!
{% endhighlight %}

What that does is saying we have a private variable named `imageView`, it is of type `UIImageView` and cannot be `nil`, and it is subject to change after initializations. If we were to change `!` to `?`, then `imageView` can become 	`nil`. This pattern forces the developer to think about `nil` checking and reduces subsequent errors. It also uses simple syntax for constant declaration, which I thought was top notch. 

In addition, a class in Swift can implement multiple protocals, but can only have one superclass. This is similar to how Java's class and interface works. 

## The App

### LinkedIn
I want to talk about LinkedIn login first. For our purpose, this app needs the user to give consent and login with their linkedin account. We used their official package for login but had trouble with the documentation sicne it was all in objective-c. Here I will share the code in Swift 4.0. 

So after you have properly configured your bundleID with LinkedIn. You can add the following to your info.plist to whitelist linkedin and add your LIAppID:

{% highlight xml %}
<key>LIAppId</key>
<string>0000000</string>
<key>LSApplicationQueriesSchemes</key>
<array>
	<string>linkedin</string>
	<string>linkedin-sdk2</string>
	<string>linkedin-sdk</string>
</array> 
{% endhighlight %}

Next, you want to let LISDK to handle transitions when coming back from the LinkedIn app. I believe LISDK is storing login sessions and user's login tokens. 

{% highlight swift %}
func application(_ app: UIApplication, open url: URL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        
        if LISDKCallbackHandler.shouldHandle(url) {
            LISDKCallbackHandler.application(app, open: url, sourceApplication: options[UIApplicationOpenURLOptionsKey.sourceApplication] as! String, annotation: options[UIApplicationOpenURLOptionsKey.annotation])
        }
        
        return true
    }
{% endhighlight %}

After that, you can code the logic for logins. you can refer to the linkedin documentation on the general information you can get with a public API. 

{% highlight swift %}
@IBAction func doLogin(sender: AnyObject) {
        LISDKSessionManager.createSession(withAuth: [LISDK_BASIC_PROFILE_PERMISSION], state: nil, showGoToAppStoreDialog: true, successBlock: {
            
            (returnState) -> Void in

            
            let url = "https://api.linkedin.com/v1/people/~:(formattedName,emailAddress,headline,summary,specialties,pictureUrls::(original),location:(name))?format=json"
            
            if LISDKSessionManager.hasValidSession() {
                LISDKAPIHelper.sharedInstance().getRequest(url, success: { (response) -> Void in
                    if response != nil {
                    // your segue here
                        self.goNext(profileInfo: response!)
                    }
                    else {
                        self.failed()
                    }
                }, error: { (error) -> Void in
                    print(error as Any)
                })
            }

        }, errorBlock: {
            (error) -> Void in
            print("Error: \(error ?? unkownError.unknown)")
        }) 
    }
{% endhighlight %}


Now the user should be able to login with LinkedIn. It will open up the LinkedIn app, perform login and take you back to your app and give you the profile info in the response. 

### The Camera

First you need to add the camera usage info in info.plist like we did before for linkedin. Next, depend on your desired implementation, add the corresponding `AVFoundation` protocal to your viewcontroller class. For example, our QR code capture class used `AVCaptureMetadataOutputObjectsDelegate` and our face detection controller used `AVCaptureVideoDataOutputSampleBufferDelegate`. 

Next, to setup a capture session and previewlayer, first declare 

{% highlight swift%}
var captureSession: AVCaptureSession!
var previewLayer: AVCaptureVideoPreviewLayer!
{% endhighlight %}

Then in `viewDidLoad`, init capture session and add inputs. Then init preview layer and add it to the original view. 

{% highlight swift%}
captureSession = AVCaptureSession()
        
        guard let videoCaptureDevice = AVCaptureDevice.default(.builtInWideAngleCamera, for: .video, position: .back) else { return }
        let videoInput: AVCaptureDeviceInput

        do {
            // Get an instance of the AVCaptureDeviceInput class using the device object.
            videoInput = try AVCaptureDeviceInput(device: videoCaptureDevice)
            
        } catch {
            // If any error occurs
            print(error)
            return
        }
        
        // Set the input device on the capture session.
        if (captureSession.canAddInput(videoInput)) {
            captureSession.addInput(videoInput)
        } else {
            failed()
            return
        }
 // Initialize the video preview layer and add it as a sublayer to the viewPreview view's layer.
        previewLayer = AVCaptureVideoPreviewLayer(session: captureSession)
        previewLayer.frame = view.layer.bounds
        previewLayer.videoGravity = .resizeAspectFill
        view.layer.addSublayer(previewLayer)
        
        captureSession.startRunning()
{% endhighlight %}

You can also configure the metadata here. For example, to setup QR codes, add the following to `viewDidLoad`:

{% highlight swift%}
//init a AVCaptureMetadataOutput and set it as the output device
        let metadataOutput = AVCaptureMetadataOutput()
        if (captureSession.canAddOutput(metadataOutput)) {
            captureSession.addOutput(metadataOutput)
            
            //set delegate and use the default queue
            metadataOutput.setMetadataObjectsDelegate(self, queue: DispatchQueue.main)
            metadataOutput.metadataObjectTypes = [.qr]
        } else {
            failed()
            return
        }
{% endhighlight %}

And then add a function:
{% highlight swift%}

func metadataOutput(_ output: AVCaptureMetadataOutput, didOutput metadataObjects: [AVMetadataObject], from connection: AVCaptureConnection) {
        captureSession.stopRunning()
        
        if let metadataObject = metadataObjects.first {
        // your actions here after getting the qrcode
            guard let readableObject = metadataObject as? AVMetadataMachineReadableCodeObject else { return }
            guard let stringValue = readableObject.stringValue else { return }
            AudioServicesPlaySystemSound(SystemSoundID(kSystemSoundID_Vibrate))
            found(code: stringValue)
        }
        
    }
{% endhighlight %}

You will do the same thing for face detections:

{% highlight swift%}
//set video outpt for the capture session
        let videoOutput = AVCaptureVideoDataOutput()
        if (self.captureSession.canAddOutput(videoOutput)){
            videoOutput.setSampleBufferDelegate(self, queue: DispatchQueue(label: "videoQueue"))
            self.captureSession.addOutput(videoOutput)
        } else {
            failed()
            return
        }
{% endhighlight %}

Here would be a good place to stop and refer to the development manual to learn about **view controller life cycles** in iOS. But anyway, add following method. It's pretty intuitive to understand.
{% highlight swift%}
override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        
        if (captureSession?.isRunning == false) {
            captureSession.startRunning()
        }
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        
        if (captureSession?.isRunning == true) {
            captureSession.stopRunning()
            captureSession = nil
        }
    }
{% endhighlight %}

Next, we need to setup request and perform for each frame in the face detection controller:
{% highlight swift%}
func captureOutput(_ output: AVCaptureOutput, didOutput sampleBuffer: CMSampleBuffer, from connection: AVCaptureConnection) {
        
        //make sure pixel buffer can be converted
        guard let pixelBuffer = CMSampleBufferGetImageBuffer(sampleBuffer) else { return }
        
        var requestOptions: [VNImageOption : Any] = [:]
        
        if let cameraIntrinsicData = CMGetAttachment(sampleBuffer, kCMSampleBufferAttachmentKey_CameraIntrinsicMatrix, nil) {
            requestOptions = [.cameraIntrinsics : cameraIntrinsicData]
        }
        
        // perform image request for face recognition
        let imageRequestHandler = VNImageRequestHandler(cvPixelBuffer: pixelBuffer, orientation: .right, options: requestOptions)
        
        do {
            try imageRequestHandler.perform(self.requests)
            tempCIImage = CIImage(cvImageBuffer: pixelBuffer)
        }
            
        catch {
            print(error)
        }
        
    }
{% endhighlight %}
 
Here, we used the Vision framework's `VNImageRequestHandler` to perform requests on hte image buffers. We used `VNDetectFaceRectanglesRequest` for our purpose, but you can do a lot more things such as getting facial landmarks with vision. It is also important here for you to properly set up the orientation and camera intrinsics. 

The request handler will perform further actions after request is complete. It will also be able to receive your VNRequest and error information. For our purpose, we are drawing bounding boxes on the detected faces. This UI update should all be done in the main dispatch queue:

{% highlight swift%}
private func handleFaces(request: VNRequest, error: Error?) {
        DispatchQueue.main.async {
            //perform all the UI updates on the main queue
            guard let results = request.results as? [VNFaceObservation] else { return }
            //further actions here
            
        }
    }
{% endhighlight %}

The results will contain a list of `VNFaceObservation` in our case. Now it is time to draw the boxes on the screen. It is important to understand `CGAffineTransform` as we will need to transform the coordinate from the camera to the UI. You can do it with the following code:

{% highlight swift%}
let transform = CGAffineTransform(scaleX: 1, y: -1).translatedBy(x: 0, y: -cameraView.bounds.height)
        
        let scale = CGAffineTransform.identity.scaledBy(x: cameraView.bounds.width, y: cameraView.bounds.height)
let facebounds = face.boundingBox.applying(scale).applying(transform)
        
//draw the box if within bounds
if (facebounds.maxY <= (previewLayer.bounds.height - scrollView.bounds.height)){
_ = createLayer(in: facebounds)
    
let tempImage = tempCIImage.oriented(forExifOrientation: 6)
    
let cropScale = CGAffineTransform.identity.scaledBy(x: tempImage.extent.width, y: tempImage.extent.height)
    
let cropFaceBounds = face.boundingBox.applying(cropScale)
	
    
faceArray.append(tempImage.cropped(to: cropFaceBounds))
}
{% endhighlight %}

`createLayer` here adds a layer to the `cameraView` to display the bounding box. You can use the following:

{% highlight swift%}
// Create a new layer drawing the bounding box
    private func createLayer(in rect: CGRect) -> CAShapeLayer {
        
        let mask = CAShapeLayer()
        mask.frame = rect
        mask.cornerRadius = 10
        mask.opacity = 0.75
        mask.borderColor = UIColor.yellow.cgColor
        mask.borderWidth = 2.0
        
        cameraView.layer.insertSublayer(mask, at: 1)

        return mask
    }
{% endhighlight %}

And there you have it! Boxes! But this is not enough. since we needed the acture pixels. Compared to simply drawing boxes around the face, this is a more costly operation, therefore we are performing this every 1 second using a `scheduledTimer`. 

You first need to understand that the buffer image we have so far is core image. To grab the pixels, we need the core graphics vesion. Since we also want to display the image at the bottom of the screen, we need UIImage as well. Also, another transform is needed here. 

{% highlight swift%}
//get the cropped ci image from facearray
tempCIImage = self.faceArray[i]
    
transform = CGAffineTransform(translationX: -tempCIImage.extent.origin.x, y: -tempCIImage.extent.origin.y)
    
//init sync
group.enter()
    
//create ui image from tempCIImage
uiImage = UIImage(ciImage: tempCIImage.transformed(by: transform))
    
//create new bitmap based image
if uiImage?.ciImage != nil {
    
newImage = self.convertCIImageToUIImage(inputImage: uiImage!.ciImage!)
    
imageView = UIImageView.init(image: newImage)
imageView.frame = CGRect.init(x: 110 * i, y: 0, width: 100, height: 100)
    
imageView.layer.cornerRadius = imageView.frame.size.height/2
imageView.layer.masksToBounds = true
imageView.layer.borderColor = UIColor.white.cgColor
imageView.layer.borderWidth = 5
    
tapGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(self.imageTapped(sender:)))
    
imageView.isUserInteractionEnabled = true
    
imageView.addGestureRecognizer(tapGestureRecognizer)
    
self.scrollView.addSubview(imageView)
    
group.leave()
}
{% endhighlight %}

One thing to note here is the use of another queue. `DispatchGroup` let you perform multithread operations and works well for repetitive things. 

And there you have it! An app that can identify and crop people's faces in a video stream...

{: .image.fit}
![workflow](/images/faceology/demo.png)
