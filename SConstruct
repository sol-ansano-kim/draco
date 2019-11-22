import sys
import os
import excons
import functools


env = excons.MakeBaseEnv()
out_basedir = excons.OutputBaseDirectory()
out_incdir = os.path.join(out_basedir, "include")
out_libdir = os.path.join(out_basedir, "lib")
staticlib = (1 if excons.GetArgument("draco-static", 1, int) != 0 else 0)


draco_opts = {}
draco_opts["BUILD_SHARED_LIBS"] = 0 if staticlib else 1


def _name(libname, static=True):
    # TODO
    if sys.platform != "win32":
        return libname

    return libname

def _path(libname, static=True):
    libpath = _name(libname, static=static)
    if sys.platform == "win32":
        libpath = libname + ".lib"
    else:
        libpath = "lib" + libname + (".a" if static else excons.SharedLibraryLinkExt())

    return os.path.join(out_libdir, libpath)

def _require(pathFunc, static=True):
    env.Append(CPPPATH=[out_incdir])
    excons.Link(env, pathFunc(static), static=static, force=True, silent=True)

DracoName = functools.partial(_name, "draco")
DracodecName = functools.partial(_name, "dracodec")
DracoencName = functools.partial(_name, "dracoenc")
DracoPath = functools.partial(_path, "draco")
DracodecPath = functools.partial(_path, "dracodec")
DracoencPath = functools.partial(_path, "dracoenc")
RequireDraco = functools.partial(DracoPath)
RequireDracodec = functools.partial(DracodecPath)
RequireDracoenc = functools.partial(DracoencPath)

headers = excons.CollectFiles(["src"], patterns=["*.h"], recursive=True)

out_headers = []
src_dir = os.path.abspath("src")
for x in headers:
    out_headers.append(os.path.join(out_incdir, os.path.relpath(os.path.abspath(x), src_dir)))


prjs = []
prjs.append({"name": "draco",
             "type": "cmake",
             "cmake-opts": draco_opts,
             "cmake-cfgs": ["CMakeLists.txt"],
             "cmake-srcs": excons.CollectFiles(["src"], patterns=["*.cc", "*.h"], recursive=True),
             "cmake-outputs": [DracoPath(static=staticlib), DracodecPath(static=staticlib), DracoencPath(static=staticlib)] + out_headers})


excons.AddHelpOptions(draco="""DRACO OPTIONS
  draco-static=0|1        : Toggle between static and shared library build [1]""")

excons.DeclareTargets(env, prjs)


Default("draco")

Export("DracoName DracodecName DracoencName DracoPath DracodecPath DracoencPath RequireDraco RequireDracodec RequireDracoenc")
