---
layout: post
title: "OpenGL ES简明教程之2"
date: 2015-04-02 15:50:23 +0800
comments: true
categories: OpenGLES,iOS
keywords: [OpenGLES,iOS,iOS Tutorial]
description: 
---

在本系统教程1中我们做好了准备，这次我们开始我们的OpenGLES的渲染。<br>

####Step 1
创建一个Single Page的iOS工程。

####Step 2
创建一个UIView的子类，比如叫EGLView.<br>
在EGLView.h中声明三个变量

```
    EAGLContext *m_context;
    GLuint m_renderbuffer;
    GLuint m_framebuffer;
```

在EGLView.m中引入OpenglES头文件

```
#import <OpenGLES/ES1/gl.h>
#import <OpenGLES/ES1/glext.h>
```

然后实现如下方法

```
+ (Class) layerClass
{
    return [CAEAGLLayer class];
}

- (id) initWithFrame: (CGRect) frame
{
    if (self = [super initWithFrame:frame]) {
        CAEAGLLayer* eaglLayer = (CAEAGLLayer*) super.layer;
        eaglLayer.opaque = YES;
        
        // step 1 创建Context
        EAGLRenderingAPI api = kEAGLRenderingAPIOpenGLES1;
        m_context = [[EAGLContext alloc] initWithAPI:api];

        if (!m_context) {
            api = kEAGLRenderingAPIOpenGLES1;
            m_context = [[EAGLContext alloc] initWithAPI:api];
        }

		// step 2 马上设置为当前Context
        if (!m_context || ![EAGLContext setCurrentContext:m_context]) {
            return nil;
        }

        if (api == kEAGLRenderingAPIOpenGLES1) {
            NSLog(@"Using OpenGL ES 1.1");
        } else {
            NSLog(@"Using OpenGL ES 2.0");
        }

		// step 3 创建render buffer 也叫 color buffer
        glGenRenderbuffersOES(1, &m_renderbuffer);
        glBindRenderbufferOES(GL_RENDERBUFFER_OES, m_renderbuffer);
        
        // step 4, 这一步一定要在step 3之后，否则会失败
        [m_context renderbufferStorage:GL_RENDERBUFFER fromDrawable: eaglLayer];
        
        
        // step 5 创建frame buffer
        glGenFramebuffersOES(1, &m_framebuffer);
        glBindFramebufferOES(GL_FRAMEBUFFER_OES, m_framebuffer);
        glFramebufferRenderbufferOES(GL_FRAMEBUFFER_OES, GL_COLOR_ATTACHMENT0_OES, GL_RENDERBUFFER_OES, m_renderbuffer);
        
        //这句可有可无
        GLenum status = glCheckFramebufferStatusOES(GL_FRAMEBUFFER_OES);
        if (status != GL_FRAMEBUFFER_COMPLETE_OES) {
            NSLog(@"err");
        }
        
        // step 6 设置 view port
        glViewport(0, 0, frame.size.width, frame.size.height);
        
        [self drawView: nil];
        m_timestamp = CACurrentMediaTime();

        CADisplayLink *displayLink = [CADisplayLink displayLinkWithTarget:self selector:@selector(drawView:)];
        [displayLink addToRunLoop:[NSRunLoop currentRunLoop] forMode:NSDefaultRunLoopMode];
    }
    return self;
}

- (void) drawView: (CADisplayLink*) displayLink
{
    
    glLoadIdentity();
    glClearColor(0.7, 0.7, 0.7, 1.0);
    glClear(GL_COLOR_BUFFER_BIT);
     
    static GLfloat vertexData[] = {-0.4f,-0.4f, 0.0f, 0.4f,-0.4f, 0.0f, 0.0f, 0.4f, 0.0f};
    glEnableClientState(GL_VERTEX_ARRAY);
    glVertexPointer(3, GL_FLOAT, 0, vertexData);
    glColor4f(1.0f, 1.0f, 0.66f, 1.0f);
    glDrawArrays(GL_TRIANGLES, 0, 3);

    [m_context presentRenderbuffer:GL_RENDERBUFFER];
}
```

####Step 3
使用EGLView<br>
在ViewController.m中的viewDidLoad中加以下代码

```
	CGRect frameSize;
    UIScreen* screen = [UIScreen mainScreen];
    CGFloat    scale = 1.0;
    if ([UIScreen instancesRespondToSelector:@selector(scale)])
    {
        scale = [screen scale];
    }
    CGRect appFrame = [screen bounds];
    frameSize       = CGRectMake(appFrame.origin.x, appFrame.origin.y, appFrame.size.width, appFrame.size.height);
    EAGLView *v = [[EAGLView alloc] initWithFrame:frameSize];
    
    [self.view addSubview:v];
```

运行吧，将会看到一个黄色的三角形，代码里有明确的注释不不多解释。<br>
glVertexPointer第一个参数表示每个顶点占几个顶点数组成员，这儿3表示点3个(因为一个点是x,y,z). 第二个参数是顶点是什么类型数据，这儿是GLfloat对应的就是GL_FLOAT, 第三个是每个顶点之间的步长，如果数据中有存color就需要设置这儿，这儿没有color，所以就设为0表示边续，最后一个参数则是存顶点数据的内存。

glDrawArrays第一个参数表示绘制的方式，看下面图就明白了，第二个表示顶点数据内存中的起始索引，这儿为0,最后一个表示顶点的个数，三角形有3个顶点，所以这儿为3.如果不明白，再仔细消化一下。<br>
![](/images/openglesdrawmodel.png)


>时间仓促，难免有不少错误，还往指正。