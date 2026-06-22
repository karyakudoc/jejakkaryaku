/**
 * =========================================================
 * JEJAK KARYAKU — BACKEND FINAL TERBARU
 * =========================================================
 */

const FOLDER_NAME = "Jejak_Karyaku_Media";

/**
 * MAIN API
 */
function doPost(e) {
  try {
    const request = JSON.parse(e.postData.contents || "{}");
    const action = request.action;

    let result = null;

    switch (action) {
      case 'getInitialData':
        result = getInitialData();
        break;
      case 'addStudent':
        result = addStudent(request);
        break;
      case 'editStudent':
        result = editStudent(request);
        break;
      case 'deleteStudent':
        result = deleteStudent(request);
        break;
      case 'uploadKarya':
        result = uploadKarya(request);
        break;
      case 'deleteKarya':
        result = deleteKarya(request);
        break;
      case 'addComment':
        result = addComment(request);
        break;
      case 'replyComment':
        result = replyComment(request);
        break;
      case 'setup':
        result = setupDatabase();
        break;
      default:
        throw new Error("Aksi tidak dikenal");
    }

    return response(true, result);

  } catch (err) {
    return response(false, null, err.toString());
  }
}

/**
 * RESPONSE
 */
function response(success, data, message) {
  return ContentService.createTextOutput(JSON.stringify({
    success,
    data: data || null,
    message: message || ""
  })).setMimeType(ContentService.MimeType.JSON);
}

/**
 * DATABASE
 */
function getDb() {
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  setupDatabase(ss);
  return ss;
}

/**
 * SETUP DATABASE
 */
function setupDatabase(existingSs) {
  const ss = existingSs || SpreadsheetApp.getActiveSpreadsheet();

  // SISWA
  let siswa = ss.getSheetByName("Siswa");
  if (!siswa) {
    siswa = ss.insertSheet("Siswa");
    siswa.appendRow(["ID", "Nama", "Pass", "Foto"]);
  }

  // KARYA
  let karya = ss.getSheetByName("Karya");
  if (!karya) {
    karya = ss.insertSheet("Karya");
    karya.appendRow(["ID_Siswa", "ID_Karya", "Judul", "URL_File", "ID_Drive", "Tanggal"]);
  }

  // PESAN
  let pesan = ss.getSheetByName("Pesan");
  if (!pesan) {
    pesan = ss.insertSheet("Pesan");
    pesan.appendRow(["ID_Pesan", "ID_Karya", "ID_Siswa", "Pesan_Wali", "Balasan_Guru", "Tanggal"]);
  }

  return true;
}

/**
 * MEDIA FOLDER
 */
function getMediaFolder() {
  const folders = DriveApp.getFoldersByName(FOLDER_NAME);
  return folders.hasNext() ? folders.next() : DriveApp.createFolder(FOLDER_NAME);
}

/**
 * GET INITIAL DATA
 */
function getInitialData() {
  const ss = getDb();

  const siswa = getRows(ss.getSheetByName("Siswa"));
  const karya = getRows(ss.getSheetByName("Karya"));
  const pesan = getRows(ss.getSheetByName("Pesan"));

  return siswa.map(s => ({
    id: s.ID,
    nama: s.Nama,
    pass: s.Pass,
    foto: s.Foto,

    karya: karya
      .filter(k => k.ID_Siswa == s.ID)
      .sort((a, b) => new Date(b.Tanggal) - new Date(a.Tanggal))
      .map(k => ({
        id: k.ID_Karya,
        judul: k.Judul,
        link: k.URL_File,
        thumb: "https://lh3.googleusercontent.com/d/" + k.ID_Drive,
        date: k.Tanggal
      })),

    percakapan: pesan
      .filter(p => p.ID_Siswa == s.ID)
      .sort((a, b) => new Date(b.Tanggal) - new Date(a.Tanggal))
      .map(p => ({
        id: p.ID_Pesan,
        workId: p.ID_Karya,
        pesanWali: p.Pesan_Wali,
        balasanGuru: p.Balasan_Guru,
        date: p.Tanggal
      }))
  }));
}

/**
 * ADD STUDENT
 */
function addStudent(req) {
  getDb().getSheetByName("Siswa").appendRow([
    req.id, req.nama, req.pass, req.foto || ""
  ]);
  return true;
}

/**
 * EDIT STUDENT
 */
function editStudent(req) {
  const sheet = getDb().getSheetByName("Siswa");
  const data = sheet.getDataRange().getValues();

  for (let i = 1; i < data.length; i++) {
    if (data[i][0] == req.id) {
      sheet.getRange(i + 1, 2).setValue(req.nama);
      sheet.getRange(i + 1, 3).setValue(req.pass);
      sheet.getRange(i + 1, 4).setValue(req.foto || "");
      break;
    }
  }
  return true;
}

/**
 * DELETE STUDENT
 */
function deleteStudent(req) {
  const ss = getDb();

  const siswaSheet = ss.getSheetByName("Siswa");
  const karyaSheet = ss.getSheetByName("Karya");
  const pesanSheet = ss.getSheetByName("Pesan");

  const siswaData = siswaSheet.getDataRange().getValues();
  for (let i = siswaData.length - 1; i >= 1; i--) {
    if (siswaData[i][0] == req.id) siswaSheet.deleteRow(i + 1);
  }

  const karyaData = karyaSheet.getDataRange().getValues();
  for (let i = karyaData.length - 1; i >= 1; i--) {
    if (karyaData[i][0] == req.id) {
      try {
        DriveApp.getFileById(karyaData[i][4]).setTrashed(true);
      } catch (e) {}
      karyaSheet.deleteRow(i + 1);
    }
  }

  const pesanData = pesanSheet.getDataRange().getValues();
  for (let i = pesanData.length - 1; i >= 1; i--) {
    if (pesanData[i][2] == req.id) pesanSheet.deleteRow(i + 1);
  }

  return true;
}

/**
 * UPLOAD KARYA
 */
function uploadKarya(req) {
  const folder = getMediaFolder();

  const blob = Utilities.newBlob(
    Utilities.base64Decode(req.fileData),
    req.mimeType || "image/jpeg",
    req.fileName
  );

  const file = folder.createFile(blob);
  file.setSharing(DriveApp.Access.ANYONE_WITH_LINK, DriveApp.Permission.VIEW);

  const fileId = file.getId();

  getDb().getSheetByName("Karya").appendRow([
    req.studentId,
    "W" + Date.now(),
    req.judul,
    "https://lh3.googleusercontent.com/d/" + fileId,
    fileId,
    new Date().toISOString()
  ]);

  return true;
}

/**
 * DELETE KARYA
 */
function deleteKarya(req) {
  const sheet = getDb().getSheetByName("Karya");
  const data = sheet.getDataRange().getValues();

  for (let i = data.length - 1; i >= 1; i--) {
    if (data[i][1] == req.workId) {
      try {
        DriveApp.getFileById(data[i][4]).setTrashed(true);
      } catch (e) {}
      sheet.deleteRow(i + 1);
    }
  }
  return true;
}

/**
 * ADD COMMENT
 */
function addComment(req) {
  getDb().getSheetByName("Pesan").appendRow([
    "C" + Date.now(),
    req.workId,
    req.studentId,
    req.comment,
    "",
    new Date().toISOString()
  ]);
  return true;
}

/**
 * REPLY COMMENT
 */
function replyComment(req) {
  const sheet = getDb().getSheetByName("Pesan");
  const data = sheet.getDataRange().getValues();

  for (let i = 1; i < data.length; i++) {
    if (data[i][0] == req.commentId) {
      sheet.getRange(i + 1, 5).setValue(req.replyText);
      break;
    }
  }
  return true;
}

/**
 * HELPER
 */
function getRows(sheet) {
  if (!sheet) return [];

  const data = sheet.getDataRange().getValues();
  if (data.length <= 1) return [];

  const headers = data[0];

  return data.slice(1).map(row => {
    const obj = {};
    headers.forEach((h, i) => obj[h] = row[i]);
    return obj;
  });
}

/**
 * MANUAL SETUP
 */
function setup() {
  setupDatabase();
}
