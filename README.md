视频video踩坑总结
1. 分段上传
网上分段上传的插件估计有很多，最出名的应该是webUplaoder,我的业务需求视频只能有一个，
上传一个就覆盖掉原来的，比较简单，所以用的是自己写的，本人能力有限，也没封装什么插件，就是实现了一个简单的分片上传，可以监控进度
  - html部分
  ```
   <input type="file" id="teaUpload" style="display: none;" accept="video/mp4" @change="handleUpload">
      <button type="primary" class="btn btn_modal" onclick="teaUpload.click()">上传视频</button>
  ```
  - js部分
  ```
  function handleUpload(e) {
    let blob = e.target.files[0] //获取文件对象
    let start = 0 //每个分片的起始尺寸
    let end      //每个分片的结束尺寸
    let bytesPerPiece = 1024*1024*10 //每个分片的大小，这里取10M
    let fileSize = blob.size
    let config = {
          headers: {
            'Content-Type': 'multipart/form-data'
          }
        },
    //分片上传有一个要求，按照顺序发送，第一份发送完了再发送第二份，因此需要用函数递归，
    //不能用循环
    uploadService(blob,start,end)
    function uploadService(blob,start,end) {
        end = start + bytesPerPiece;
        if (end > filesize) {
            end = filesize;
        }
        //大文件分片
        let chunk = blob.slice(start, end)
        let formData = new FormData()
         //每个文件需要有一个独一无二的token来标志，可以简单的使用时间戳来拼凑
        formData.append('token', token)
        formData.append('totalSize', filesize)
        formData.append('start', start)
        formData.append('end', end)
        formData.append("file", chunk)
        //这里使用axios来发送请求，也可以使用原生的ajax,以表单传数据的格式来传递formData，
        //需要传递第三个参数config
        axios.post(url,formData,config).then(res=>{
          if(success){
            start = end
            if(start>=fileSize) {
              alert('视频上传完毕')
              //视频上传完毕之后的一些处理
              return
            }else{
              percent = start/fileSize //制作进度条
            }
          }else {
            alert('视频上传出错')
            return
          }
        }).catch(()=>{
            alert('视频上传出错')
            return
        })
    }
  }
  ```
