<?php
if(isset($_POST['testsubmit']))
{
  

     
    // $recipient_email    = 'ecscholarshiptest@gmail.com'; //recipient email (most cases it is your personal email)
    $recipient_email    = 'vinita@rieltechnologies.com'; //recipient email (most cases it is your personal email)
    
    //Capture POST data from HTML form and Sanitize them, 
    // $sender_name    = $_POST["sender_name"]; //sender name
    $subject        = "Scholarship"; //get subject from HTML form
 
    
    $name           = $_POST["name"];
    $dob            = $_POST["dob"];
    $gender         = $_POST["gender"];
    $reply_to_email = $_POST["email"];
    $center         = $_POST["center"];//
   

    $message  = "Candidate Name:    ".$name."\r\n";
    $message  .= "Date Of Birth:    ".$dob."\r\n";
    $message  .= "Gender:    ".$gender."\r\n";
    $message  .= "Email:    ".$reply_to_email."\r\n";
    $message  .= "Center:    ".$center."\r\n";
   


    /* //don't forget to validate empty fields 
    if(strlen($sender_name)<1){
        die('Name is too short or empty!');
    } 
    */
    
    //Get uploaded file data
    $file_tmp_name    = $_FILES['my_file']['tmp_name'];
    $file_name        = $_FILES['my_file']['name'];
    $file_size        = $_FILES['my_file']['size'];
    $file_type        = $_FILES['my_file']['type'];
    $file_error       = $_FILES['my_file']['error'];

    // if($file_error > 0)
    // {
    //     die('Upload error or No files uploaded');
    // }
    //read from the uploaded file & base64_encode content for the mail
    $handle = fopen($file_tmp_name, "r");
    $content = fread($handle, $file_size);
    fclose($handle);
    $encoded_content = chunk_split(base64_encode($content));

        $boundary = md5("RIEL");
        //header
        $headers = "MIME-Version: 1.0\r\n"; 
        $headers .= "From:".$reply_to_email."\r\n"; 
        $headers .= "Reply-To: ".$reply_to_email."" . "\r\n";
        $headers .= "Cc: ".$reply_to_email."" . "\r\n"; 
        $headers .= "Content-Type: multipart/mixed; boundary = $boundary\r\n\r\n"; 
        
        //plain text 
        $body = "--$boundary\r\n";
        $body .= "Content-Type: text/plain; charset=ISO-8859-1\r\n";
        $body .= "Content-Transfer-Encoding: base64\r\n\r\n"; 
        $body .= chunk_split(base64_encode($message)); 
        
        //attachment
        $body .= "--$boundary\r\n";
        $body .="Content-Type: $file_type; name=".$file_name."\r\n";
        $body .="Content-Disposition: attachment; filename=".$file_name."\r\n";
        $body .="Content-Transfer-Encoding: base64\r\n";
        $body .="X-Attachment-Id: ".rand(1000,99999)."\r\n\r\n"; 
        $body .= $encoded_content; 
    
    $sentMail = @mail($recipient_email, $subject, $body, $headers); 
    if($sentMail) //output success or failure messages
    {       
        die('Thank you for Registration');
    }else{
        die('Could not send mail! Please check your PHP mail configuration.');  
    } 
 
}
?>
