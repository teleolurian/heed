_       = require 'lodash'
exec    = require('child_process').exec
fs      = require 'fs'
path    = require 'path'
rm      = require('rimraf').sync

PATHS = root: path.resolve('.'), bin: path.resolve('./node_modules/.bin')
PATHS.targets = PATHS.root + '/targets'
PATHS.build   = PATHS.targets + '/build'
PATHS.ws      = PATHS.root + '/workspace'

dir = (k, s = '') ->
  s = "/#{s}" unless s.length == 0 or s.substr(0,1) == '/'
  (PATHS[k] ? PATHS.root) + s

refresh = (args...) ->
  rm dir(args...)
  fs.mkdirSync dir(args...)

task 'build', 'Build App', ->
  invoke 'compile:assets'
  exec "cp #{PATHS.ws}/index.html #{PATHS.build}"

task 'compile', 'Build Executables', ->
  invoke 'build'
  exec "cp ./package.json #{PATHS.build}"
  NwBuilder = require 'node-webkit-builder'
  nw = new NwBuilder
    files:        dir 'build', '**/**'
    platforms:    ['osx64']
    buildDir:     PATH.targets + '/bin'
    cacheDir:     Path.targets + '/cache'
  nw.on 'log', console.log
  nw.build().then -> console.log 'done'
    .catch console.error

task 'compile:assets', 'Build assets', ->
  invoke 'compile:assets:js'
  invoke 'compile:assets:css'

task 'compile:assets:js', 'Build js from coffee', ->
  refresh 'build', 'js'
  exec "cp ./node_modules/jquery/dist/jquery.min.js #{dir 'build', 'js/jquery.min.js'}"
  exec "coffee -c -o #{dir 'build', 'js'} #{dir 'ws', 'scripts'}"


task 'compile:assets:css', 'Build CSS from stylus', ->
  refresh 'build', 'css'
  exec dir('bin', 'stylus') + " -c -o #{dir 'build','css'} #{dir 'ws', 'styles/main.styl'}"
