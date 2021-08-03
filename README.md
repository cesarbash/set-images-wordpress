# set-images-wordpress

<?php

    function catch_that_image($content) {
        // global $post, $posts;
        $first_img = '';
        ob_start();
        ob_end_clean();
        $output = preg_match_all('/<img.+src=[\'"]([^\'"]+)[\'"].*>/i', $content, $matches);
        $first_img = $matches [1] [0];
    
        if(empty($first_img)){ //Defines a default image
        $first_img = "no hay";
        }
        return $first_img;
    }

    $posts =  get_publications_for( [ 
        'category_name' => 'materiales-de-eventos',
        'posts_per_page' => 28,
    ] ) -> data;

    foreach( $posts as $index => $post ){

        if($post->images->full){

        }else{
            // var_dump($post);
            var_dump($post->id);
            var_dump($post->title);
            $content_post = get_post($post->id);
            $content = $content_post->post_content;
            $image = catch_that_image($content);
            // var_dump($content);
            // echo $image;

            $getImageFile = $image;
            $wp_filetype = wp_check_filetype( $getImageFile, null );

            $attachment_data = array(
                'post_mime_type' => $wp_filetype['type'],
                'post_title' => sanitize_file_name( $getImageFile ),
                'post_content' => '',
                'post_status' => 'inherit'
            );

            $attach_id = wp_insert_attachment( $attachment_data, $getImageFile, $post->id );
            require_once( ABSPATH . 'wp-admin/includes/image.php' );
            $attach_data = wp_generate_attachment_metadata( $attach_id, $getImageFile );
            wp_update_attachment_metadata( $attach_id, $attach_data );

            $result =  set_post_thumbnail( $post->id, $attach_id );
            var_dump($result);
        }
        // echo $post -> id.' ';
        // echo $post -> title;
    }
?>
