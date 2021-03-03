<div>

```python
{{ body | safe }}

```
Run python script via webassembly: <button onClick="runPython(this)">Run</button>

<script type="text/javascript">

    function outputStuff(elem, s) {
        var output = elem.parentNode.parentNode.querySelector(".output");
        
        output.innerHTML = s;
        output.style.display = "block";
    };

    function runPython(elem) {
        languagePluginLoader.then(() => {

            var setup_code = `
import sys, io, traceback
namespace = {}  # use separate namespace to hide run_code, modules, etc.
def run_code(code):
  """run specified code and return stdout and stderr"""
  out = io.StringIO()
  oldout = sys.stdout
  olderr = sys.stderr
  sys.stdout = sys.stderr = out
  try:
      # change next line to exec(code, {}) if you want to clear vars each time
      exec(code, namespace)
  except:
      traceback.print_exc()

  sys.stdout = oldout
  sys.stderr = olderr
  return out.getvalue()
`
            pyodide.runPythonAsync(setup_code)

            var code = elem.parentNode.parentNode.firstElementChild.firstElementChild.textContent;
            pyodide.globals.code_to_run = code;

            pyodide.runPythonAsync("run_code(code_to_run)")
                    .then(output => outputStuff(elem, output))
                    .catch((err) => { outputStuff(elem, err) })
        });
        
    }
</script>

<div class="output" style="background-color: black; color: #FFFFFF; white-space: pre-line; display: none">a</div>

</div>