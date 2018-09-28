#重定向
浏览器知道这个过程
用法:
`req.sendRedirect(path);` path可以是相对或绝对路径


#分发
对于浏览器是透明的
`req.getRequestDispatcher(path).forward(req, resp);;` path可以是相对或绝对路径
`getServletContext().getRequestDispatcher(path).forward(req, resp);` 通过ServletContext分发只能是绝对路径

##forward include
forward 一去不复返。
include 处理完还得回来接着处理。