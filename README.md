# Codeigniter-Image-Rotate-Solution-while-uploading-from-mobile
Rotate the image using exif data



 $config['upload_path'] = 'uploads/profiles';
				$config['allowed_types'] = 'jpg|jpeg|gif|png';
				$new_name = time().$_FILES["profile_pic"]['name'];
				$config['file_name'] = $new_name;
				
                $this->load->library('upload', $config);

                   //check if a file is being uploaded
                   if(strlen($_FILES["profile_pic"]["name"])>0){
                
                       if ( !$this->upload->do_upload("profile_pic"))//Check if upload is unsuccessful
                       {
                           $error = array('error' => $this->upload->display_errors());
                           print_r($errors);
                       }
                       else
                       {
                
                           $config['image_library'] = 'gd2';
                           $config['source_image'] = $this->upload->upload_path.$this->upload->file_name;
                           $filename = $_FILES['profile_pic']['tmp_name'];
                
                
                           $imgdata=exif_read_data($this->upload->upload_path.$this->upload->file_name, 'IFD0');
                           
                
                           list($width, $height) = getimagesize($filename);
                           if ($width >= $height){
                               $config['width'] = 400;
                           }
                           else{
                               $config['height'] = 400;
                           }
                           $config['master_dim'] = 'auto';
                
                
                           $this->load->library('image_lib',$config);
                
                           if (!$this->image_lib->resize()){  
                               echo "error";
                           }else{
                
                               $this->image_lib->clear();
                               $config=array();
                
                               $config['image_library'] = 'gd2';
                               $config['source_image'] = $this->upload->upload_path.$this->upload->file_name;
                
                
                               switch($imgdata['Orientation']) {
                                   case 3:
                                       $config['rotation_angle']='180';
                                       break;
                                   case 6:
                                       $config['rotation_angle']='270';
                                       break;
                                   case 8:
                                       $config['rotation_angle']='90';
                                       break;
                               }
                
                               $this->image_lib->initialize($config);
                               $this->image_lib->rotate();
                
                           }
                      }      
                  

					$post['profile_thumbnail'] = 'uploads/profiles/'.$new_name;    
					$post['profile_pic'] = 'uploads/profiles/'.$new_name;
					
