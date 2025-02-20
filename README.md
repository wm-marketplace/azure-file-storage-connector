## Connector Introduction
Connector is a Java based backend extension for WaveMaker applications. Connectors are built as Java modules & exposes java based SDK to interact with the connector implementation.
Each connector is built for a specific purpose and can be integrated with one of the external services. Connectors are imported & used in the WaveMaker application. Each connector runs on its own container thereby providing the ability to have it’s own version of the third party dependencies.

## Features of Connectors
1. Connector is a java based extension which can be integrated with external services and reused in many Wavemaker applications.
2. Each connector can work as an SDK for an external system.
3. Connectors can be imported once in a WaveMaker application and used many times in the applications by creating multiple instances.
4. Connectors are executed in its own container in the WaveMaker application, as a result there are no dependency version conflict issues between connectors.

## About Azure File Storage Connector

### Introduction
Azure file storage mainly can be used if we want to have a shared drive between two servers or across users. In the Azure file storage structure, the first thing we need to have is an Azure storage account. Azure file storage is offered under the Azure storage account. And once we have created an Azure storage account, we'll create a file share.

We can create an unlimited number of file shares within a storage account. Once we create a file share, then we can create directories, just like folders, and then we can upload files into it.

Azure File storage (https://azure.microsoft.com/en-in/services/storage/files/)  connector provides simplified APIs to work with azure file storage to store & retrieve files from Azure Storage Service. Using this connector, one can build the Upload, Download, List & Delete operations of files in Azure storage and integrate with WaveMaker application.

### Prerequisite
1. Azure Storage access with connectionString
2. Java 1.8 or above
4. Maven 3.1.0 or above
5. Any java editor such as Eclipse, Intelij..etc
6. Internet connection

### Build

You can build this connector using following command
```Java
mvn clean install
```
### Deploy

You can import connector dist/azure-file-storage-connector.zip artifact in WaveMaker studio under file explorer.On after uploading into 
wavemaker, you can update your profile properties such connectionString.

### Using Azure File Storage Connector in WaveMaker
### step1
1. Download the latest zip [here](https://github.com/wavemaker/azure-file-storage-connector/releases)
2. Import the downloaded Azure File Storage Connector zip into your app using the Import Resource option in the Connector folder.  ![image](https://github.com/user-attachments/assets/df95de69-0767-4dab-8ebf-d270daeade6d)
   ![image](https://github.com/user-attachments/assets/e3d74e39-8c23-4081-9cc9-161940f0a113)
### Step 2: Configure azurefilestorage properties in profiles
1.By default externalized connector properties are added in the project profiles.
2.To work with azure file storage connector provide the connectionString given by azure in profiles as shown below
<img width="958" alt="image" src="https://github.com/user-attachments/assets/7e9943c5-dda8-434e-baeb-deb4beba26d3" />

### Step3: File Operations in Storage Account.
1.Import the following statements
```
import  org.springframework.web.multipart.MultipartFile;
import java.io.IOException;
import java.io.InputStream;
import java.io.ByteArrayInputStream;
import com.wavemaker.connector.azurefilestorage.model.AzureFile;
import java.util.List;
import com.wavemaker.runtime.file.model.DownloadResponse;
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
```
2. Autowire the Connector Service into the added JavaService.
```
@Autowired
private AzureFileStorageConnector azureFileStorageConnector;
```
3.Create File Share in Storage Account.
```
    private static final String shareName="<sharename>";

    @PostConstruct
    public void init(){
        azureFileStorageConnector.createShare(shareName); 
    }
```
4.Upload file to Azure File Storage.
```
public void uploadFile(String filePath, MultipartFile file){
    try (InputStream inputStream = new ByteArrayInputStream(file.getBytes())) {
        azureFileStorageConnector.upload(shareName, filePath,inputStream);
        } catch (IOException e) {
            throw new RuntimeException("Exception occurred while uploading file: "+e);
        }
}
```
5.List Files from Azure File Storage.

```
public List<AzureFile> listFiles(String shareName, String directoryPath){
    return azureFileStorageConnector.listFiles(shareName,directoryPath);
}
```
6.Download File from Azure File Storage
```
public DownloadResponse downloadFile(String filePath){
    String fileName=filePath.substring(filePath.lastIndexOf('/')+1,filePath.length());
    AzureFileResponse data= azureFileStorageConnector.downloadFileWithProperties(shareName,filePath);
    DownloadResponse downloadResponse=new DownloadResponse();
    ByteArrayOutputStream outStream = (ByteArrayOutputStream)data.getOutputStream();
    downloadResponse.setContents(new ByteArrayInputStream(outStream.toByteArray()));
    downloadResponse.setInline(false);
    downloadResponse.setFileName(fileName);
    downloadResponse.setContentType(data.getContentType());
    return downloadResponse;
}
```
7.Delete File from Azure File Storage
```
public void deleteFile(String shareName, String filePath){
    azureFileStorageConnector.deleteFile(shareName,filePath);
}
```

### step4 : upload file to Azure storage using File upload widget.
1.Drop File upload widget.

2.Create Variable for uploadFile added in the Javaservice in above Step.

     a.Switch to Data tab and bind the required inputs needed for the variable.
   
     b.click on file input bind icon and select the file upload widget selectedFiles.(File upload selectedFiles gives info about the list of selected files)
      ``` Widgets.fileupload1.selectedFiles[0] ```
   
     c.click on bind icon of the filePath input and give the upload directory path of the azure storage along with fileName.
      ``` "files/"+Widgets.fileupload1.selectedFiles[0].name ```   where files is the directory path in the azure storage.
   
3.Drop a button on the canvas and onClick event of the button call the created variable.
   


  

