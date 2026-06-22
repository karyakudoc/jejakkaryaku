const DB = {
  SISWA: "Siswa",
  KARYA: "Karya",
  PESAN: "Pesan",
  USER: "Users"
};

const CACHE_KEY = "JEJAK_V3";
const CACHE_TIME = 300;

const MEDIA_FOLDER = "Jejak_Karyaku_Media";
const BACKUP_FOLDER = "Jejak_Karyaku_Backup";

const OPENAI_KEY =
  PropertiesService.getScriptProperties()
    .getProperty("OPENAI_KEY");function json(success, data, message) {
  return ContentService
    .createTextOutput(
      JSON.stringify({
        success: success,
        data: data || null,
        message: message || ""
      })
    )
    .setMimeType(ContentService.MimeType.JSON);
}

function uid(prefix) {
  return prefix + Date.now();
}

function sha256(text) {
  return Utilities.computeDigest(
    Utilities.DigestAlgorithm.SHA_256,
    text
  )
  .map(b =>
    ('0' + (b & 0xff).toString(16)).slice(-2)
  )
  .join('');
}function getRows(sheet) {
  if (!sheet) return [];

  const data = sheet.getDataRange().getValues();

  if (data.length <= 1) {
    return [];
  }

  const headers = data[0];

  return data.slice(1).map(r => {
    const obj = {};

    headers.forEach((h, i) => {
      obj[h] = r[i];
    });

    return obj;
  });
}function getCache() {
  return CacheService.getScriptCache();
}

function clearCache() {
  getCache().remove(CACHE_KEY);
}function doGet() {
  return ContentService
    .createTextOutput("JEJAK KARYAKU API AKTIF 🚀");
}function doPost(e) {
  try {
    const req = JSON.parse(
      e.postData.contents || "{}"
    );

    const map = {
      getInitialData,
      loginUser,
      verifyPin,
      addStudent,
      uploadKarya,
      addComment,
      getStatistics,
      runBackup,
      generateAIComment
    };

    const fn = map[req.action];

    if (!fn) {
      throw new Error("Action tidak ditemukan");
    }

    return json(true, fn(req));

  } catch (err) {
    return json(
      false,
      null,
      err.toString()
    );
  }
}function loginUser(req) {
  const users =
    getRows(
      SpreadsheetApp.getActive()
        .getSheetByName(DB.USER)
    );

  const user =
    users.find(
      u => u.Username == req.username
    );

  if (!user) {
    return null;
  }

  if (
    user.PasswordHash !==
    sha256(req.password)
  ) {
    return null;
  }

  return {
    id: user.ID,
    nama: user.Nama,
    role: user.Role,
    kelas: user.Kelas
  };
}function verifyPin(req) {
  const siswa =
    getRows(
      SpreadsheetApp.getActive()
        .getSheetByName(DB.SISWA)
    );

  const s =
    siswa.find(
      x => x.ID == req.studentId
    );

  if (!s) {
    return false;
  }

  return s.PinHash === sha256(req.pin);
}function addStudent(req) {
  const sheet =
    SpreadsheetApp.getActive()
      .getSheetByName(DB.SISWA);

  sheet.appendRow([
    uid("S"),
    req.nama,
    sha256(req.pin),
    req.foto || "",
    req.kelas || "",
    new Date()
  ]);

  clearCache();

  return true;
}function uploadKarya(req) {
  const folder =
    getMediaFolder();

  const blob =
    Utilities.newBlob(
      Utilities.base64Decode(
        req.fileData
      ),
      "image/jpeg",
      req.fileName
    );

  const file =
    folder.createFile(blob);

  file.setSharing(
    DriveApp.Access.ANYONE_WITH_LINK,
    DriveApp.Permission.VIEW
  );

  SpreadsheetApp
    .getActive()
    .getSheetByName(DB.KARYA)
    .appendRow([
      uid("K"),
      req.studentId,
      req.judul,
      file.getUrl(),
      file.getId(),
      new Date()
    ]);

  clearCache();

  return true;
}function addComment(req) {
  SpreadsheetApp
    .getActive()
    .getSheetByName(DB.PESAN)
    .appendRow([
      uid("C"),
      req.workId,
      req.studentId,
      req.text,
      "",
      new Date()
    ]);

  clearCache();

  return true;
}function getStatistics() {
  const ss =
    SpreadsheetApp.getActive();

  return {
    siswa:
      ss.getSheetByName(DB.SISWA)
        .getLastRow() - 1,

    karya:
      ss.getSheetByName(DB.KARYA)
        .getLastRow() - 1,

    komentar:
      ss.getSheetByName(DB.PESAN)
        .getLastRow() - 1
  };
}function runBackup() {
  const ss =
    SpreadsheetApp.getActive();

  const blob =
    ss.getBlob();

  const folders =
    DriveApp.getFoldersByName(
      BACKUP_FOLDER
    );

  const folder =
    folders.hasNext()
      ? folders.next()
      : DriveApp.createFolder(
          BACKUP_FOLDER
        );

  folder.createFile(
    blob.setName(
      "Backup_" +
      Utilities.formatDate(
        new Date(),
        Session.getScriptTimeZone(),
        "yyyyMMdd_HHmm"
      ) +
      ".xlsx"
    )
  );

  return true;
}function getMediaFolder() {
  const folders =
    DriveApp.getFoldersByName(
      MEDIA_FOLDER
    );

  if (folders.hasNext()) {
    return folders.next();
  }

  return DriveApp.createFolder(
    MEDIA_FOLDER
  );
}function generateAIComment(req) {
  if (!OPENAI_KEY) {
    return "OPENAI_KEY belum diatur.";
  }

  const prompt =
    "Buat komentar guru SD yang positif untuk karya berjudul: " +
    req.title;

  const res =
    UrlFetchApp.fetch(
      "https://api.openai.com/v1/chat/completions",
      {
        method: "post",
        contentType:
          "application/json",
        headers: {
          Authorization:
            "Bearer " +
            OPENAI_KEY
        },
        payload:
          JSON.stringify({
            model: "gpt-4o-mini",
            messages: [
              {
                role: "user",
                content: prompt
              }
            ]
          })
      }
    );

  const data =
    JSON.parse(
      res.getContentText()
    );

  return data
    .choices[0]
    .message.content;
}
