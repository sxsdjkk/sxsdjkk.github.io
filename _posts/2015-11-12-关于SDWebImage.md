---
layout: post
title:  "关于SDWebImage"
date:   2015-11-12 02:20:42 +0800
categories: jekyll update
---

####SDWebImage功能很强大
    
如果对一个UIButton或者UIImageView赋图片，可以直接使用SDWebImage中得类扩展方法，如
{% highlight objC %}
- (void)sd_setImageWithURL:(NSURL *)url;
{% endhighlight %}

但有时候又需要对获取到的图片进行处理，那就可能需要用到下列两种方法，


###1.在completationBlock中实现

例如：
{% highlight objC %}
/**
 * Set the imageView `image` with an `url`.
 *
 * The download is asynchronous and cached.
 *
 * @param url            The url for the image.
 * @param completedBlock A block called when operation has been completed. This block has no return value
 *                       and takes the requested UIImage as first parameter. In case of error the image parameter
 *                       is nil and the second parameter may contain an NSError. The third parameter is a Boolean
 *                       indicating if the image was retrived from the local cache or from the network.
 *                       The fourth parameter is the original image url.
 */
- (void)sd_setImageWithURL:(NSURL *)url completed:(SDWebImageCompletionBlock)completedBlock;
{% endhighlight %}

在completedBlock中可以获得image进行处理。


###2.有时候需要在赋图之前对图片处理，比如图片加密的情况。这时候可以结合SDImageDownloader和SDImageCache处理

下面是SDImageDownLoader的最主要方法，
{% highlight objC %}
/**
 * Creates a SDWebImageDownloader async downloader instance with a given URL
 *
 * The delegate will be informed when the image is finish downloaded or an error has happen.
 *
 * @see SDWebImageDownloaderDelegate
 *
 * @param url            The URL to the image to download
 * @param options        The options to be used for this download
 * @param progressBlock  A block called repeatedly while the image is downloading
 * @param completedBlock A block called once the download is completed.
 *                       If the download succeeded, the image parameter is set, in case of error,
 *                       error parameter is set with the error. The last parameter is always YES
 *                       if SDWebImageDownloaderProgressiveDownload isn't use. With the
 *                       SDWebImageDownloaderProgressiveDownload option, this block is called
 *                       repeatedly with the partial image object and the finished argument set to NO
 *                       before to be called a last time with the full image and finished argument
 *                       set to YES. In case of error, the finished argument is always YES.
 *
 * @return A cancellable SDWebImageOperation
 */
- (id <SDWebImageOperation>)downloadImageWithURL:(NSURL *)url
                                         options:(SDWebImageDownloaderOptions)options
                                        progress:(SDWebImageDownloaderProgressBlock)progressBlock
                                       completed:(SDWebImageDownloaderCompletedBlock)completedBlock;
{% endhighlight %}
这个方法中可以获得image或者data进行处理，然后可以用SDImageCache进行存储
{% highlight objC %}
/**
 * Store an image into memory and disk cache at the given key.
 *
 * @param image The image to store
 * @param key   The unique image cache key, usually it's image absolute URL
 */
- (void)storeImage:(UIImage *)image forKey:(NSString *)key;

{% endhighlight %}
这个方法时SDImageCache最常用的方法，其中key可以是自定义的字符串，一般都是图片的url。

##说完图片的存储，再说一下图片的读取，

SDWebImageManager的方法{% highlight objC %}
- (BOOL)diskImageExistsForURL:(NSURL *)url;
{% endhighlight %}和SDImageCache的方法{% highlight objC %}
- (BOOL)diskImageExistsWithKey:(NSString *)key;
{% endhighlight %}可以判断本地图片是否存在。

而SDImageCache的方法{% highlight objC %}
- (UIImage *)imageFromDiskCacheForKey:(NSString *)key;
{% endhighlight %}可以直接从沙盒读取图片。






