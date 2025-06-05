<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<title>Formulário - Levantamento de Impressões</title>
<style>
  body { font-family: Arial; background-color: #f5f5f5; margin: 20px;}
  .container { background: white; padding: 20px; border-radius: 10px; max-width: 800px; margin: auto; box-shadow: 0 0 10px rgba(0,0,0,0.1);}
  h2 { text-align: center;}
  label { display: block; margin-top: 15px; font-weight: bold;}
  input[type="text"], textarea { width: 100%; padding: 8px; margin-top: 5px; border: 1px solid #ccc; border-radius: 5px;}
  textarea { resize: vertical; }
  input[type="checkbox"], input[type="radio"] { margin-right: 5px; margin-top: 10px;}
  .subfield { margin-left: 20px; display: none;}
  button { margin-top: 20px; background-color: #4CAF50; color: white; padding: 10px 15px; border: none; border-radius: 5px; cursor: pointer;}
  button:hover { background-color: #45a049;}
  .checkbox-group div { margin-bottom: 8px; }
</style>
</head>
<body>
<div class="container">
  <h2>Levantamento de Documentos Impressos</h2>

  <form id="documentForm">
    <label>Nome:</label>
    <input type="text" name="nome" required>

    <label>Setor:</label>
    <input type="text" name="setor" required>

    <label>Quais tipos de documentos você costuma imprimir com mais frequência?</label>

    <div class="checkbox-group">
      <div>
        <input type="checkbox" name="relatorios" value="Sim" onclick="toggleField('relatoriosField')"> Relatórios
        <div class="subfield" id="relatoriosField">
          <label>Quais?</label>
          <textarea name="relatorios"></textarea>
        </div>
      </div>

      <div>
        <input type="checkbox" name="formularios" value="Sim" onclick="toggleField('formulariosField')"> Formulários
        <div class="subfield" id="formulariosField">
          <label>Quais?</label>
          <textarea name="formularios"></textarea>
        </div>
      </div>

      <div>
        <input type="checkbox" name="ordens" value="Sim" onclick="toggleField('ordensField')"> Ordens de Serviço
        <div class="subfield" id="ordensField">
          <label>Quais?</label>
          <textarea name="ordens"></textarea>
        </div>
      </div>

      <div>
        <input type="checkbox" name="assinatura" value="Sim" onclick="toggleField('assinaturaField')"> Documentos para assinatura
        <div class="subfield" id="assinaturaField">
          <label>Quais?</label>
          <textarea name="assinatura"></textarea>
        </div>
      </div>

      <div>
        <input type="checkbox" name="externos" value="Sim" onclick="toggleField('externosField')"> Documentos externos
        <div class="subfield" id="externosField">
          <label>Quais?</label>
          <textarea name="externos"></textarea>
        </div>
      </div>

      <div>
        <input type="checkbox" name="planilhas" value="Sim" onclick="toggleField('planilhasField')"> Planilhas
        <div class="subfield" id="planilhasField">
          <label>Quais?</label>
          <textarea name="planilhas"></textarea>
        </div>
      </div>

      <div>
        <input type="checkbox" name="comunicados" value="Sim" onclick="toggleField('comunicadosField')"> Comunicados
        <div class="subfield" id="comunicadosField">
          <label>Quais?</label>
          <textarea name="comunicados"></textarea>
        </div>
      </div>

      <div>
        <input type="checkbox" name="outros" value="Sim" onclick="toggleField('outrosField')"> Outros
        <div class="subfield" id="outrosField">
          <label>Quais?</label>
          <textarea name="outros"></textarea>
        </div>
      </div>
    </div>

    <label>Com que frequência você realiza impressões?</label>
    <input type="radio" name="frequencia" value="Diariamente" required> Diariamente<br>
    <input type="radio" name="frequencia" value="Semanalmente"> Semanalmente<br>
    <input type="radio" name="frequencia" value="Quinzenalmente"> Quinzenalmente<br>
    <input type="radio" name="frequencia" value="Raramente"> Raramente<br>
    <input type="radio" name="frequencia" value="Não utilizo impressão no meu setor"> Não utilizo impressão no meu setor

    <label>Existe algum documento que poderia ser digital?</label>
    <input type="radio" name="digital" value="Sim" onclick="toggleField('quaisDigitaisField')"> Sim
    <div class="subfield" id="quaisDigitaisField">
      <label>Quais?</label>
      <textarea name="quaisDigitais"></textarea>
    </div>
    <input type="radio" name="digital" value="Não" onclick="hideField('quaisDigitaisField')"> Não

    <label>Sugestão sobre o uso das impressões na empresa:</label>
    <textarea name="sugestao" rows="4"></textarea>

    <button type="submit">Enviar</button>
  </form>
  <p id="status"></p>
</div>

<script>
  function toggleField(id) {
    const field = document.getElementById(id);
    field.style.display = (field.style.display === "block") ? "none" : "block";
    if (field.style.display === "none") {
      const input = field.querySelector('textarea, input');
      if (input) input.value = '';
    }
  }

  function hideField(id) {
    const field = document.getElementById(id);
    field.style.display = "none";
    const input = field.querySelector('textarea, input');
    if (input) input.value = '';
  }

  const form = document.getElementById('documentForm');
  const status = document.getElementById('status');

  form.addEventListener('submit', e => {
    e.preventDefault();
    const formData = new FormData(form);
    const data = {};

    formData.forEach((value, key) => {
      if (data[key]) {
        data[key] += ', ' + value;
      } else {
        data[key] = value;
      }
    });

    fetch('https://script.google.com/macros/s/AKfycbxX1_LKFjGBAMkjHjJjzp8IGAx7wujCD210jbz3hORJocZFNxwVfKthLa3GxPv04OhJ/exec', {
      method: 'POST',
      body: JSON.stringify(data)
    })
    .then(response => {
      if(response.ok){
        status.innerText = "✅ Enviado com sucesso!";
        form.reset();
        document.querySelectorAll('.subfield').forEach(div => div.style.display = 'none');
      } else {
        status.innerText = "❌ Erro ao enviar. Verifique a URL do Script e permissões.";
      }
    })
    .catch(error => {
      status.innerText = "❌ Erro ao enviar.";
      console.error(error);
    });
  });
</script>
</body>
</html>
