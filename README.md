# UIImageView-Download

 Lazy load for images. Download images and save them locally, while showing it inside the UIImageView, if still available.
     If a local image already exists it will be loaded immediately, then the download will start and the local image will be updated.
     
     ```
     func downloadImageFrom(link: URL, contentMode: UIView.ContentMode, savePath: String, manageError: ManageDownloadImageError?) {
        
        if let localImage = UIImage(contentsOfFile: savePath) {
            self.image = localImage
            self.contentMode = contentMode
            self.backgroundColor = .white
        }
        
        URLSession.shared.dataTask(with: link, completionHandler: { (data, _, _) -> Void in
            
            DispatchQueue.main.async { [weak self] in
                
                if let imageData = data,
                    let downloadedImage = UIImage(data: imageData) {
                    
                    self?.contentMode =  contentMode
                    self?.image = downloadedImage
                    self?.backgroundColor = .white
                    
                    DispatchQueue.global(qos: .background).async {
                        do {
                            let url = URL(fileURLWithPath: savePath)
                            try downloadedImage.jpegData(compressionQuality: 1.0)?.write(to: url)
                            
                        } catch {
                            
                            manageError?(.localSave)
                            
                        }
                    }
                    
                }else{
                    
                    manageError?(.download)
                    
                }
            }
        }).resume()
    }
}
     ```
